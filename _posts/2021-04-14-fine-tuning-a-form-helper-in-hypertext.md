---
layout: post
title: Fine-Tuning a Form Helper in Hypertext
---

In a [previous post][fhh] I put together a simple form helper with the Hypertext DSL in pure Ruby. It operated from a “form” data structure put together from a Scrivener validation object.

This was the Hypertext DSL definition:

```
form action: f[:action], method: f[:method] do
  f[:fields].each do |ff|
    label for: ff[:id] do
      text ff[:label]
    end
    input type: ff[:type], name: ff[:name], id: ff[:id], value: ff[:value]
  end
  input type: "submit", value: f[:submit]
end
```

And this was the computed data structure (output of `RegistrationForm#form`):

```
{
  action: "/contact",
  method: "post",
  submit: "send",
  errors: {},
  fields: [
    {
      type: :email,
      name: :email,
      label: "Email",
      id: "email_field",
      value: nil,
      attribute: :email
    },
    {
      type: :text,
      name: :name,
      label: "Name",
      id: "name_field",
      value: nil,
      attribute: :name
    }
  ]
}
```

Now, one of the benefits of writing your HTML in pure Ruby is that you get to use all of the conveniences of your programming language within your “templates”.

Notice looping over the form fields:

```
f[:fields].each do |ff|
  …
end
```

It’s just Ruby. No need for dedicated templating syntax or for tags for interpolation. This is what most attracted me to this approach after having used Elm’s HTML library.

One of the things we can do, thanks to our judicious naming of field attributes, is use the power of Ruby to simplify the definition of our attributes.

The field is a Hash with symbol keys. And crucially, the keys in the Hash correspond directly to the attribute names in the HTML. So we can therefore pass the necessary subset of the field Hash directly as the first argument to the Hypertext DSL methods.

So the following call to the `input` method would be transformed from:

```
input type: ff[:type], name: ff[:name], id: ff[:id], value: ff[:value]
```

to:

```
input ff.slice(:type, :name, :id, :value)
```

`Hash#slice` returns a hash containing only the given keys and their values. Instead of writing out a new hash, duplicating the relevant fields, we simply pass a slice of the original hash.

What we could do for other aspects of the form to make things as smooth as we have for this `input` would be to transform our “form object” (strictly speaking a data structure rather than an object) so that we can implement this slice pattern with everything in the form.

We’ll have to duplicate the value for `field[:id]` assigning it to `field[:for]` so that the label can work properly. That then allows for the following, also slicing for the `form` attributes:

```
form f.slice(:action, method) do
  f[:fields].each do |ff|
    label ff.slice(:for) do
      text ff[:label]
    end
    input ff.slice(:type, :name, :id, :value)
  end
  input type: "submit", value: f[:submit]
end
```

This works a charm:

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
  <input type="submit" value="send" />
</form>
```

The one trade-off is with the submit button. It doesn’t follow the same pattern as the other fields. It doesn’t have a label for starters, and it doesn’t have the same attributes as the other inputs.

If we add this field to the list of fields on our form hash then we can experiment to see if this works:

```
…
{
  type: :text,
  name: :name,
  label: "Name",
  id: "name_field",
  value: nil,
  attribute: :name
},
{ 
  type: :submit,
  value: "send",
}
```

We need to make the rendering of the label conditional to ensure it doesn’t appear for the submit button.

```diff
  form f.slice(:action, method) do
    f[:fields].each do |ff|
+     unless ff[:label].nil?
        label ff.slice(:for) do
          text ff[:label]
        end
+     end
      input ff.slice(:type, :name, :id, :value)
    end
-   input type: "submit", value: f[:submit]
  end
```

What does that result in?

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
  <input type="submit" value="send" />
</form>
```

Happy days. We rely on `Hash#slice` only returning keys and values for keys that are actually present in the hash. That is, for the submit button, although we specify `:name` and `:id` keys to `Hash#slice`, since these are not present in the hash for that particular field definition, they don’t get returned and thus do not appear in the final HTML output.

Now this is a simple example with limited form elements and inputs. I wonder if this scales to the full gamut of HTML form elements.

Nevertheless, it goes a long way in showing that the combination of a simple rendering engine alongside a declarative form leads to a very neat and concise solution in this particular case.

—*Wednesday 14th April 2021.*

[fhh]: https://www.crossingtheruby.com/2021/04/13/a-form-helper-in-hypertext.html
