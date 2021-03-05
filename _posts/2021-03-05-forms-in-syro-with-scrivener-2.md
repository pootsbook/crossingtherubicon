---
layout: post
title: Form Helpers in Syro, Mote and Scrivener (2)
---

Previously we extracted a [data structure from a Scrivener validation object][svo] which can act as the basis for the rendering of a form. We only needed to add a little extra information to the data which was otherwise computed from the configuration of the Scrivener object.

This is what the data structure looks like, encapsulated in the `form` method of our Scrivener object:

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

What this allows us to do is to render an HTML form programmatically using the data available. Essentially, to write a form helper, or helpers, to be able to do this on a broader scale and avoid the rote practice of writing the HTML form by hand each time. Similar in purpose to the ActionView FormBuilder in Rails.

This straddles the line between the templating engine and raw string generation. For this example I’ll do a bit of both, both creating a Mote partial and leaning on string generation for the creation of our form helper. Mixing these is perhaps not something sensible for consistency but it demonstrates the two approaches well. For brevity I’ll limit the helpers to the form without errors, and just helpers for the `input`, leaving out the `textarea`.

Again, this isn’t meant to be the optimal solution for this particular case. I’m proving what’s possible and how straightforward it is to start to think about these problems yourself.

This is the desired HTML:

```
<form action="/contact" method="post">

  … errors …
  
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
  
  … textarea …
  
  <input type="submit" value="Send">
</form>
```

The HTML itself also mirrors the structure of our form data. In one sense all we’re doing is writing a data transformation from one representation to another. 

I think it helps to emphasise this because often when we come to concepts we know well, like HTML, we don’t necessarily think of them in a context other than that which is most familiar to us. And really all we have to do is switch context. That is, HTML, here, is not so much relating to structure and layout as it is to a programmatic representation of a data structure. That jump often escapes me; I’ve got to change my lens.

Our deck in Syro, responsible for rendering with Mote, looked like this:

```
class Web < Syro::Deck
  include Mote::Helpers
  
  def render(template, params = {})
    res.html mote("views/#{template}.mote", params)
  end
end
```

Let’s add a Mote partial for the form itself, and a helper method for an input. First the partial, which will take a local variable of `form` which will be the data structure from our Scrivener object:

```
{% raw %}
# views/_form.mote
<form 
  action="{{form.action}}" 
  method="{{form.method}}">
  
  % form.fields.each do |field|
    render_field(field)
  % end
  
  <input type="submit" value="{{form.submit}}">
</form>
{% endraw %}
```

The Mote template approach interpolates the values of the variables and we’re still writing HTML by hand. We’ll now write the `render_field` helper method in the deck which will be concatenating strings in Ruby methods. This is what we’re going for:

```
<label for="email_field">
  Email Address
</label>
<input 
  type="email" 
  name="email" 
  id="email_field" 
  value="">
```
 
```
def render_field(field)
  %Q(<label for="#{field.id}">
    #{field.label}
  </label>
  <input 
    type="#{input_type(field.type)}" 
    name="#{field.name}" 
    id="#{field.id}" 
    value="#{field.value}">)
end

def input_type(type)
  { text_input: "text" }
end
```

To be honest there’s not much difference here between the two approaches. What if we wanted to take a more HTML-ish route? That is build up yet another abstraction layer more faithful to HTML. Say an input helper takes a list of attributes and optional content a little like Elm:

```
def render_field(field)
  [ render_tag(:label, { for: field.id }, field.label),
    render_tag(:input, { 
      type: field.type, 
      name: field.name, 
      id: field.id, 
      value: field.value })
  ].join("\n")
end

def render_tag(tag_name, attributes = {}, content = nil)
  html = "<#{tag_name}"
  attributes.each do |key, value|
    html << %Q(#{key}="#{value} ")
  end
  if !content.nil?
    html << ">#{content}</#{tag_name>"
  else
    html << ">"
  end
end

def input_type(type)
  { text_input: "text" }
end
```

This looks promising and has the beginnings of something reusable. We’ve dealt with one form and one input. Of course, if you were to cover the full range of HTML then there would be a lot more to consider. But this is a half-way house that does our job straightforwardly, allows for reusability, and avoids the complexity of heavier solutions.


—*Friday 5th March 2021.*

[svo]: https://www.crossingtheruby.com/2021/03/04/forms-in-syro-with-scrivener.html
