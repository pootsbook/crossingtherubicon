---
layout: post
title: Form Helpers in Syro, Mote and Scrivener
---

In a previous post I hooked up Scrivener with Mote and Syro for [validating a form][scr]. The HTML for the form is quite verbose and I’d like to demonstrate one way in which I tackled that.

The solution is very targeted at this specific purpose and thus should not be taken as an all-encompassing HTML form generation library but it certainly points one path towards that end.

What I think is useful is that it shows us some of the pieces required for such a library and one way of putting them together. It builds largely on our Scrivener object, extending it to cover a mapping between field and input type alongside the resulting HTML generation.

To jog our memories, this is the Scrivener object we ended up with for a contact form:

```
class ContactForm < Scrivener
  attr_accessor :name
  attr_accessor :email
  attr_accessor :message
  
  def validate
    assert_present :name
    assert_present :email
    assert_present :message
    
    assert_email :email
  end
end
```

And this was the HTML:

```
<form action="/contact" method="post">
  <ul>
    % contact_form.errors.each do |error|
      <li>
        {{ error.first }}: {{error.last.join(",")}}
      </li>
    % end
  </ul>

  <label for="name_field">
    Name
  </label>
  <input 
    type="text" 
    name="name" 
    id="name_field" 
    value="{{contact_form.name}}">
  
  <label for="email_field">
    Email Address
  </label>
  <input 
    type="email" 
    name="email" 
    id="email_field" 
    value="{{contact_form.email}}">
  
  <label for="message_field">
    Message
  </label>
  <textarea 
    name="message" 
    id="message_field" 
    value="{{contact_form.message}}">
  </textarea>
  
  <input type="submit" value="Send">
</form>
```

If we take a step back then we see that this form has several attributes: an action, a method, a value for the submit button, a list of form fields and a list of errors. (Form fields could also be grouped with their respective errors, but for now we’ll keep them separate.)

```
form:
  action: "/contact"
  method: "post"
  errors: []
  fields: []
  submit: "Send"
```

We know from Scrivener that an error is a tuple (expressed as a two element Array in Ruby) with a symbol corresponding to an attribute, and a list of symbols corresponding to a set of validation errors.

```
form:
  action: "/contact"
  method: "post"
  submit: "Send"
  errors: [[:email, [:not_present]]]
  fields: []
```

If we operate under the assumption that there will be a one-to-one mapping between attributes defined on a Scrivener object and fields in the form, then we can begin to fill out the structure of a field. Looking at the HTML for the email field:

```
<label for="email_field">
  Email Address
</label>
<input 
  type="email" 
  name="email" 
  id="email_field" 
  value="{{contact_form.email}}">
```

We can see there are different elements that we need to account for. The `for` attribute of `label`, and the `id`, `name`, and `value` attributes of `input` can all be derived from the Scrivener attribute name. The text content of `label` could also be derived, at a push; let’s do that for simplicity. And the key information that is not in Scrivener at all is the `type` attribute of the `input`.

So a rough YAML structure for all of the form data, including fields, now looks something like this (I’m using symbols for fixed elements corresponding to an exact HTML attribute value versus strings for variable values):

```
form:
  action: "/contact"
  method: "post"
  submit: "Send"
  errors: [[:email, [:not_present]]]
  fields:
  - type: :email_input  # Extra information
    name: "email"       # Derived from Scrivener attribute
    label: "Email"      # Title-cased Scrivener object attribute
    id: "email_field"   # Derived from Scrivener attribute
    value: ""           # Scrivener attribute value
    attribute: :email   # Scrivener attribute
```

Now all that remains is to encode the information that we don’t already have in the Scrivener object. Then we can simply apply a transformation on the Scrivener object to an HTML string. There are different ways that we could do this so I’ll keep it simple for now, simply reproducing the structure above in a `form` method. 

```
class ContactForm < Scrivener
  attr_accessor :name
  attr_accessor :email
  attr_accessor :message
  
  def validate
    assert_present :name
    assert_present :email
    assert_present :message
    
    assert_email :email
  end
  
  def form
    {
      action: "/contact",
      method: "post",
      submit: "send",
      errors: errors,
      fields: attributes.keys.map do |attr|
        {
          type: fieldmapping[attr],
          name: attr,
          label: attr.capitalize,
          id: "#{attr}_field",
          value: send(attr),
          attribute: attr
        }
      end
    }
  end
  
  def field_mapping
    {
      name: :text_input,
      email: :email_input,
      message: :textarea
    }
  end
end
```

The `field_mapping` method (or rather Hash) maps a given attribute to a fixed HTML form field tag. In this case `:input_text` to `<input type="text">`. We still have to write the rendering but this is the one leap that I’ve made and, leaving aside the form details (`action`, `method`, and `submit`), is the only element of a field that is coupled in any way to a concrete HTML representation.

If you have a keen eye, you will notice that what we have done is lift out the form definition as data using a Scrivener object as a base. Once it is encoded as data then it is simply another step to render it to an HTML representation. We’ll take care of that in a future post.

—*Thursday 4th March 2021.*

[scr]: https://www.crossingtheruby.com/2021/02/28/integrating-scrivener-with-syro.html
