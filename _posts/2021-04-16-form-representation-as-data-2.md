---
layout: post
title: "Form Representation as Data (2)"
---

In the [previous post][frd] I thought about how best we could represent a form as a data structure and how this (obviously?) ended up looking a lot like abstract HTML syntax. I played around with ways to render this in a generic way using Hypertext DSL and broadened out from just input to include textarea which has different properties.

I arrived at a method which looked like this, after umming and ahing at the best representation of a form as data:

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

The field data structure passed to such a message looked like this:

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

I flattened the label into the field definition and switched on the `type` attribute in the rendering method’s case statement.

A case statement always seems to be a compromise in Ruby for my tastes (despite their abundant usage in Elm and Elixir—certainly a lot to do with pattern matching).

It struck me that I’m calling these DSL methods on self and we might be able to raise the different cases to be more dynamic with `public_send`. In this post I want to walk down that route to see where it takes us.

Let’s leave both `label` and `textarea` alone to begin with and just focus on `input`. The `tag` attribute in the field corresponds to the HTML tag of the field, and also happens to correspond to the method name in the Hypertext DSL since it has a one-to-one correlation with HTML.

We can then replace the static call to `input` with a dynamic call, passing the value of our `tag` attribute:

```diff
  f = {
    …
    tag: "input"
  }

- input f.slice(:type, :name, :id, :value)
+ public_send f[:tag], f.slice(:type, :name, :id, :value)
```

This then makes it generic for all (form field) tag types. If we seek to integrate `textarea` then we notice that it has different properties, firstly a different set of attributes, and secondly it contains a text node while `input` is self-closing and therefore does not.

Let’s see how far we can go. A simple translation of our `textarea` is as follows: 

```diff
- textarea f.slice(:id, :name, :rows, :cols) do
-   text f[:value]
- end
+ public_send f[:tag], f.slice(:id, :name, :rows, :cols) do
+  text f[:value]
+ end
```

We therefore need to reconcile both of those areas where `input` and `textarea` differ to arrive at something truly generic. Let’s start with the attributes.

We’ll give our renderer some insight into the type of attributes that each form field element has. To keep it simple for now we’ll just focus on those we have listed above. It will be little work to expand the list to the full set of possible attributes.

```
ATTRS = {
  "input" => %i(type name id value),
  "textarea" => %i(id name rows cols)
}
```

Then we can simply splat those into the call to `slice`.

```
# input
public_send f[:tag], f.slice(*ATTRS.fetch(f[:tag]))

# textarea
public_send f[:tag], f.slice(*ATTRS.fetch(f[:tag])) do
  text f[:value]
end
```

That’s one down, two to go. Aside from the block the call is identical. For the text node I’m going to cheat a little. If we think back to yesterday’s separate ‘denormalised’ `label` definition:

```
label: {
  for: "email_field",
  text: "Email"
}
```

We had an explicit `text` attribute. Let’s duplicate the `value` attributes value into a `text` attribute for the `textarea`.

```
f = {
  label: "Message",
  for: "message_field",
  tag: "textarea",
  name: "message",
  id: "message_field",
  value: "",
  text: ""
}
```

Knowing that a self-closing tag will not have a `text` element we can then write as follows:

```
public_send f[:tag], f.slice(*ATTRS.fetch(f[:tag])) do
  text f[:text] unless f[:text].nil?
end
```

We know that the block will return `nil` and thus refrain from adding anything to the eventual HTML.

Taking a fresh look at our `render_field` method yields the following code:

```
def render_field(f)
  label f.slice(:for) do
    text f[:label]
  end unless f[:label].nil?
  public_send f[:tag], f.slice(*ATTRS.fetch(f[:tag])) do
    text f[:text] unless f[:text].nil?
  end
end
```

Well, I thought it would work, but we end up with closing input tags which is not what we want.

```
<input type="email" name="email" id="email_field" value="">
</input>
```

So passing the block, irrespective of its return value means a closing tag is created. This is when I pine for my explicit list of self-closing tags which dictated whether or not to take the block versus the presence or absence of the block dictating whether it is closing or self-closing respectively.

Back to the drawing board. We’ll then have to switch on the presence of the `text` attribute. I’m not going to lie, I’m shedding a tear.

```
def render_field(f)
  label f.slice(:for) do
    text f[:label]
  end unless f[:label].nil?
  if f[:text]
    public_send f[:tag], f.slice(*ATTRS.fetch(f[:tag])) do 
      text f[:text] 
    end
  else
    public_send f[:tag], f.slice(*ATTRS.fetch(f[:tag])) 
  end
end
```

That at least does the trick. Although it feels a little dissatisfying. I’m not sure how I can avoid the conditional.

What I have spotted though is that with this machinery, label is just another field that could go through the generic method call if label were defined separately. Let’s resurrect that.

```
field = {
  label: {
    text: "Email",
    for: "email_field"
  },
  input: {
    type: "email",
    name: "email",
    id: "email_field",
    value: ""
  }
}
```

And then we can update our attribute lists:

```
ATTRS = {
  "input" => %i(type name id value),
  "label" => %i(for),
  "textarea" => %i(id name rows cols)
}
```

And modify our `render_field` method accordingly.

```
def render_field(f)
  f.each do |tag, att|
    if att[:text]
      public_send tag, att.slice(*ATTRS.fetch(tag)) do 
        text att[:text] 
      end
    else
      public_send tag, att.slice(*ATTRS.fetch(tag)) 
    end
  end
end
```

And that brings us full circle. The output of Scrivener object with name, email and message fields looks like this:

```
<form action="/contact" method="post">
  <label for="email_field">
    Email
  </label>
  <input type="email" name="email" id="email_field" value="" />
  <label for="name_field">
    Name
  </label>
  <input type="text" name="name" id="name_field" value="" />
  <label for="message_field">
    Message
  </label>
  <textarea id="message_field" name="message">
    Hello, World!
  </textarea>
  <input type="submit" value="send" />
</form>
```

The full script is available as a [gist][ref]. Although I’m still smarting about the conditional in my `render_form` method, I’m happy that I’ve managed to make rendering generic and this should offer a foundation for expansion to other HTML form field elements. 

Although the next one I select might throw a spanner in the works.

—*Friday 16th April 2021.*

[frd]: https://www.crossingtheruby.com/2021/04/15/form-representation-as-data.html
[ref]: https://gist.github.com/pootsbook/35feac04cb8b708b9f3a2432b20d9723
