---
layout: post
title: "Basic Form: Bootstrap Form"
---

Yesterday we had a look at the construction of a [basic form in Rails][bfr]. We are using standard Bootstrap 4.6 HTML. I mentioned we could clean up a few things by abstracting them a little. The good news is Bootstrap Form has already done so.

It may seem unfair to pick up Bootstrap Form for a form that we have written in Bootstrap but on the contrary this is exactly what we’re trying to achieve. A means of abstracting away the details to make a form definition as straightforward as possible.

Bootstrap Form operates on the same principles as Rails, using the in-built form builder as a foundation. It goes further than what you get out of the box, handily providing error and help text support.

The form object will remain identical to what we defined in Rails. This is how we’d go about writing the template for that form.

```
<%= bootstrap_form_for @form, 
    as: :subscription, 
    url: email_subscriptions_path do |f| %>
  <%= f.email_field :email, 
      label: "Email address", 
      help: "We'll never share your email with anyone else." %>
  <%= f.primary "Submit" %>
<% end %>
```

Nice and tidy. Everything for one form-control in one method call. The variable data lifted into clear options (`label` and `help`).

Just like its predecessor, this is relying on deep introspection on the form/model object to gather the attribute value and the errors, if present. The rest is using Rails conventions for `name`, `id`, and `for` attributes.

Bootstrap Form contains a list of all input types, defining a method for each type that can be used in the view. It defines the HTML with Rails template helpers like `content_tag` and supplying options with a Ruby hash and either using custom option keys for library functionality or standard HTML attribute option keys to be passed down to the method rendering the HTML.

—*Friday 18th June 2021.*

[bfr]: https://www.crossingtheruby.com/2021/06/17/basic-form-rails.html
