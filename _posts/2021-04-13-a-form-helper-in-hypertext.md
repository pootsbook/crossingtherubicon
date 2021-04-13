---
layout: post
title: A Form Helper in Hypertext
---

Before I embarked on [the journey][tj] that led to the adoption and extension of [Hypertext][ht] I had built a [basic renderer][br] using Mote for a form helper on top of Scrivener.

I would like to revisit the rendering of the form and use the Hypertext DSL.

As a reminder, this is the Scrivener object which acts as a basis for the form, with the `form` method encapsulating a data structure that can be used for rendering:

```
class RegistrationForm < Scrivener
  attr_accessor :name
  attr_accessor :email
  
  def validate
    assert_present :name
    assert_present :email
    
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
          type: field_mapping[attr],
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
      name: :text,
      email: :email,
    }
  end
end
```

And here is the desired HTML:

```
<form action="/contact" method="post">
  
  <label for="name_field">
    Name
  </label>
  <input 
    type="text" 
    name="name" 
    id="name_field" 
    value="">
  
  <label for="email_field">
    Email Address
  </label>
  <input 
    type="email" 
    name="email" 
    id="email_field" 
    value="">
  
  <input type="submit" value="Send">
</form>
```

If we assume a helper method inside a `Template` object which inherits from the `Hypertext::DSL` class similar to that arrived at in [Hypertext DSL: reading (nested) templates from file][hdr] then it offers us a sensible starting point:

```diff
  class Template < Hypertext::DSL
    def initialize(site)
      site.each_pair do |key, val|
        instance_variable_set(sprintf("@%s", key), val)
      end
      
      @ht = Hypertext.new
-     render @layout
+     render_form @form
    end
    
    def render(string)
      instance_eval string
    end
    
+   def render_form(form)
+     …
+   end
  end
```

We are able to call the DSL methods in this method in order to construct a definition that will later be rendered to HTML.

There are a couple of ways to approach this. One is to convert the desired HTML output into Hypertext and then modify the bits to be generated programmatically with the right values. The other is to start with the form data structure and build it up programmatically from the start.

I think I’ll opt for the latter as it seems like it will mean less rework.

We can start relatively easily, the first thing I’ll do is rename the parameter `f` to avoid clashing with the Hypertext DSL method `form`. 

```
def render_form(f)
  form do
  end
end
```

Then we can set the attributes `action` and `method` on the form, taking the values from our form data:

```
def render_form(f)
  form action: f[:action], method: f[:method] do
  end
end
```

We’ll also add the submit button at the bottom of the form:

```
def render_form(f)
  form action: f[:action], method: f[:method] do
    input type: "submit", value: f[:submit]
  end
end
```

Now we’ll want to get started with the fields themselves:

```
def render_form(f)
  form action: f[:action], method: f[:method] do
    f[:fields].each do |ff|
      label for: ff[:id] do
        text ff[:label]
      end
      input type: ff[:type], name: ff[:name], id: ff[:id], value: ff[:value]
    end
    input type: "submit", value: f[:submit]
  end
end
```

And that’s pretty much it. How does that fare? Let’s run it as follows:

```
validator = RegistrationForm.new({})
template  = Template.new(form: validator.form)
puts template.to_s
```

It doesn’t run:

```
lib/hypertext.rb:45:in `escape': undefined method
`gsub' for :Email:Symbol (NoMethodError)
```

So it looks like we’re passing a symbol where Hypertext is expecting a string. Let’s put that right in our `RegistrationForm` class.

```diff
  class RegistrationForm < Scrivener
    …
    def form
      …
      fields: attributes.keys.map do |attr|
         { 
           type: field_mapping[attr],
           name: attr,
-          label: attr.capitalize,
+          label: attr.capitalize.to_s,
           id: "#{attr}_field",
           value: send(attr),
           attribute: attr
         }
    end
    …
  end
```

We need to convert the label to a string since that is being passed to the `text` Hypertext DSL method.

If we rerun then we get precisely what we’re looking for:

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

I really like it. A form rendered by writing pure Ruby. No interpolation, no switching contexts, just Ruby, Ruby, Ruby.

—*Tuesday 13th April 2021.*

[tj]: https://www.crossingtheruby.com/2021/03/06/a-small-html-helper-library.html
[ht]: https://www.crossingtheruby.com/2021/03/14/hypertext-write-html-in-ruby.html
[br]: https://www.crossingtheruby.com/2021/03/05/forms-in-syro-with-scrivener-2.html
[hdr]: https://www.crossingtheruby.com/2021/04/06/hypertext-dsl-reading-nested-templates-from-file-2.html
