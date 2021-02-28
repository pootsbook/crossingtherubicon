---
layout: post
title: Integrating Scrivener with Syro
---

As minimal tools Scrivener and Syro complement each other well. Syro handles routing and Scrivener is good for validating input from forms. Mote can also be used on the templating side.

In this post we’ll build a simple contact form consisting of a name, an email address and a message which we want to validate before submission.

Let’s use Scrivener to build a filter, which, for simplicity’s sake, we’ll call a form:

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

The Scrivener `ContactForm` class is nicely declarative. It’s easy to see at a glance what is going on.

We’ll also create an HTML form for our view/template, and we’ll do it without dressing it up in any way for clarity:

```
<form action="/contact" method="post">
  <label for="name_field">
    Name
  </label>
  <input type="text" name="name" id="name_field">
  
  <label for="email_field">
    Email Address
  </label>
  <input type="email" name="email" id="email_field">
  
  <label for="message_field">
    Message
  </label>
  <textarea name="message" id="message_field">
  </textarea>
</form>
```

This form will be served up by Syro at `/contact` (GET) and we can post the form to the same endpoint (POST):

```
Syro.new do
  on "contact" do
    get do
      contact_form = ContactForm.new({})
      render("contact", form: contact_form)
    end
    
    post do
      contact_form = ContactForm.new(req.params)
      if contact_form.valid?
        # send an email with contact_form.attributes OR
        # save the details to a data store AND
        # redirect to a thank you page
      else
        render("contact", form: contact_form)
      end
    end
  end
end
```

I’ll explain a rendering setup in a future post, but for now, let’s just assume that the `render` method takes the template and renders it with the variables passed through in the second argument.

This means that we now have `contact_form` available to us in the template and we should probably make use of it to display any error messages and to pre-populate the value of any form fields:

```
<form action="/contact" method="post">
  <ul>
    % contact_form.errors.each do |error|
      <li>
        {% raw %}{{ error.first }}: {{error.last.join(",")}}{% endraw %}
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
    {% raw %}value="{{contact_form.name}}">{% endraw %}
  
  <label for="email_field">
    Email Address
  </label>
  <input 
    type="email" 
    name="email" 
    id="email_field" 
    {% raw %}value="{{contact_form.email}}">{% endraw %}
  
  <label for="message_field">
    Message
  </label>
  <textarea 
    name="message" 
    id="message_field" 
    {% raw %}value="{{contact_form.message}}">{% endraw %}
  </textarea>
</form>
```

It’s all very straightforward, and indeed if you work with Rails or Phoenix, the pattern is very familiar. The construction of the form HTML may seem a little verbose and tedious but as I’ll show in a future post, it doesn’t have to be that way. I’d like to show how you can arrive at the same convenience yourself without having to rely on a host of external framework helpers.

At the very least you can see how Scrivener would integrate with a web framework or routing library. In this example I chose Syro, but you could just as easily use Scrivener with Rails, as an alternative to ActiveModel in cases where you are not hitting the database.

—*Sunday 28th February 2021.*


