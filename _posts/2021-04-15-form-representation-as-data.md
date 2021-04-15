---
layout: post
title: Form Representation as Data
---

When [fine-tuning][ft] a form helper written in Hypertext (or actually, just Ruby) I stumbled upon a simple trick to use the form and field definition to directly define the HTML attributes of the respective elements using `Hash#slice`.

This raised the question for me as to whether a form definition is in its purest sense nothing other than a declarative description of HTML.

That is, the structure of the form and the field, mirror in many ways the HTML layout of the form. To a certain extent this is expected, but the level of overlap did strike me.

To further explore this, I’m going to reverse engineer a form definition from the following HTML.

```
<form action="/contact" method="post">
  <label for="email_field">
    Email
  </label>
  <input type="email" name="email" id="email_field" value="" />
  <input type="submit" value="send" />
</form>
```

I’ll group the label within the field.

```
form = {
  action: "/contact",
  method: "post",
  fields: [
    {
      label: {
        for: "email_field",
        text: "Email"
      },
      input: {
        type: "email",
        name: "email",
        id: "email_field",
        value: ""
      }
    },
    {
      input: {
        type: "submit",
        value: "send"
      }
    }
  ]
}  
```

This is not too far off where we finished with yesterday. The main difference is the extraction of the label as a separate entity in the field itself with `form.field[].label.text` and `form.field[].label.for` instead of the flattened `form.field[].label` and `form.field[].for`.

The former, from this post, is truer to the HTML structure, but is this a good thing? I don’t know.

I know that it would allow me to clean up the conditional, let’s extract some of the code into separate helper methods using the newer structure. Here is what we start off with:

```
def render_form(f)
  form f.slice(:action, method) do
    f[:fields].each do |ff|
      unless ff[:label].nil?
        label ff.slice(:for) do
          text ff[:label]
        end
      end
      input ff.slice(:type, :name, :id, :value)
    end
  end
end
```

If we introduce a `render_label` method then this can encapsulate the label logic:

```
def render_label(l)
  label l.slice(:for) do
    text l.slice(:text)
  end unless l.nil?
end
```

Although rewriting this has brought to my attention that since this is just Ruby we can inline the conditional I’m not sure that this gives us much over keeping everything in the `render_form` method. If we have to write a `render_<tag>` method for each tag then it kind of defeats the purpose of introducing Hypertext. Maybe not.

If we just do the inlining of the conditional in the original method then it looks just as clean:

```diff
  def render_form(f)
    form f.slice(:action, method) do
      f[:fields].each do |ff|
-       render_label(ff[:label])
+       label ff[:label].slice(:for) do
+         text ff[:label][:text]
+       end unless ff[:label].nil?
        input ff.slice(:type, :name, :id, :value)
      end
    end
  end
```

There doesn’t seem to be a great deal of benefit in extracting the label out separately here.

The other direction to go which would allow for expanding our helper to different form element types would be to have a case statement switching on the case of the form element. As far as I can remember we have:

```
input
textarea
select

progress
meter
```

The `input` has a range of different types, divided (by me) into an arbitrary group of common and uncommon types:

```
text
email
password
file
hidden
checkbox
radio
submit
button

color
date
datetime-local
image
month
number
range
reset
search
tel
time
url
week
```

I think this would favour the the flattened field definition. We want a tag and a set of attributes. The attributes for each tag will be unique to the tag itself. Maybe something like this:

```
{
  label: "Email",
  for: "email_field",
  tag: "input",
  type: "email",
  name: "email",
  id: "email_field",
  value: ""
}
```

And then we could have a `render_field` method catering in this case for both an `input` and a `textarea`:

```
def render_field(f)
  label f.slice(:for) do
    text f[:label]
  end unless f[:label].nil?
  case f[:tag]
  when 'input'
    input f.slice(:type, :name, :id, :value)
  when 'textarea'
    textarea f.slice(:id, :name, :rows, :cols) do
      text f[:value]
    end
  end
end
```

This would allow us to cater for the full range. It doesn’t look especially elegant, I think there is an even better way to accomplish this, but I’ll leave that to a future post.

—*Wednesday 14th April 2021.*

[ft]: https://www.crossingtheruby.com/2021/04/14/fine-tuning-a-form-helper-in-hypertext.html
