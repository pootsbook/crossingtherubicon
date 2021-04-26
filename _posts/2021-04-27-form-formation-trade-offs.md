---
layout: post
title: "Form Formation: Trade-offs"
---

In [yesterday’s post][yp] I observed that we can look at form generation in an imperative or a declarative fashion.

Choosing one approach over the other brings with it several trade-offs that I’d like to investigate in this post.

The current state of play, in the Ruby world at least, favours imperative approaches, building the form in the HTML and where convenient adding helpers into the mix. The HTML is then the place where the UI and the form object get “hooked up”.

In it’s simplest form, with a templating library like Mote, we could configure a form with a field as follows:

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
    value="{% raw %}{{name}}{% endraw %}">
    
  <input type="submit" value="Send">
</form>
```

We interpolate all of the necessary form details into the template as part of the HTML declarations.

In Rails, we wrap up some of the components into form helpers which use the builder pattern, taking the model object and calling out to it internally to get the required information. A similar form would look like this in ERB:

```
<%= form_for @contact do |f| %>
  <ul>
    <% @contact.errors.each do |error| %>
      <li><%= error.full_message %></li>
    <% end %>
  </ul>
  
  <%= f.label :name, "Name" %>
  <%= f.text_field :name %>
  
  <%= f.submit "Send" %>
<% end %>
```

The helpers remove some of the boilerplate by hiding the connection between a field and its corresponding value on the model object. This lends a concision thanks to convention.

Nevertheless, the creation of the form is still imperative. The fields and corresponding controls still need to be typed out and referenced correctly for everything to work.

A declarative solution could be as little as this:

```
<%= form(@form) %>
```

So why do we go the imperative route here? I think there are several good reasons, but the main reason I believe is flexibility.

First of all, there is a certain simplicity and familiarity that HTML brings. To depart from the simple declaration of HTML elements (or the wrapping of them in corresponding helpers) is to move one step away from the eventual output.

Everything that you can do in HTML, you can do here. There are no constraints, no barriers, no hindrances to doing precisely what you want. You are working at the coal face and the full HTML toolbox is available to you. 

If an individual form needs something different, or unique, or if the layout needs to be altered, then this is easy to accomplish. If you are imperatively constructing your HTML for the rest of the application, then doing so for a form is just as straightforward. You might not even consider it an inconvenience.

A declarative solution, in principle, is something that is comparatively inflexible. If you declare how a form should look once, then it is difficult to change for an individual case that departs from the norm. A declarative form is “baked” so good luck picking apart the ingredients to reform them.

So at what point does the flexible/inflexible trade-off pay off? If all of your forms are different, and each form needs its own unique flavour, then it doesn’t make sense to go down a declarative path.

If however you are using a strict design system, and all forms are going to look the same, and each type of individual form control is identical across all forms, then an imperative approach quickly seems like a lot of work.

If you can guarantee that level of predictability, if you choose for a standard approach, you can reduce the complexity and repetitiveness of typing out the same form HTML each time.

If your form can be described declaratively with data then you can define your forms in a much less verbose syntax than HTML. And if your layout or design for a certain form control changes, you can update the HTML representation once, and all of your forms benefit. It is a level of abstraction that makes you resistant to style and markup changes.

It also opens up the door for a greater level of automation. Perhaps similar in some ways to the original Rails scaffolds. What if you could define a data model together with the form representation and get an application up and running in no time?

The abstraction and automation also point towards a greater level of testability. Being able to test the workings of the most important source of (data) interactions that users will have with your application at a level that is far more forgiving than end-to-end or browser tests. If you can guarantee that the HTML representation works, then each form declaration can be tested independently of the HTML representation lending greater confidence in the working state of your application.

With some of these trade-offs in mind I want to further explore why declarative forms seem so attractive to me.

—*Tuesday 27th April 2021.*

[yp]: https://www.crossingtheruby.com/2021/04/26/form-formation-imperative-versus-declarative.html
