---
layout: post
title: "Basic Form: Simple Form"
---

Creating a form with Simple Form is almost identical to Formtastic. This is no surprise given the explicit inspiration for the DSL. 

```
<%= simple_form_for @form,
    as: :subscription,
    url: email_subscriptions_path do |f| %>
  <%= f.input :email,
      label: "Email address",
      hint: "We'll never share your email with anyone else." %>
  <%= f.button :submit, "Submit", class: "btn-primary" %>
<% end %>
```

Something that is possible in both libraries, but that I didn’t mention the [Formtastic][ft] post, is that you can further simplify the form definition by handling UI text with i18n configuration.

```
en:
  simple_form:
    labels:
      subscription:
        email: "Email address"
    hints:
      subscription:
        email: "We'll never share your email with anyone else."
  helpers:
    submit:
      subscription:
        create: "Submit"
        edit: "Submit"
```

This lifts the UI text away from the view and into the i18n configuration and the library proper. (The `helpers` key, used for defining the button text, is default Rails.)

The form could then be defined:

```
<%= simple_form_for @form,
    as: :subscription,
    url: email_subscriptions_path do |f| %>
  <%= f.input :email %>
  <%= f.button :submit, class: "btn-primary" %>
<% end %>
```

We’ve reduced the form all the way down to the point where it even feels unnecessary to write anything in the template apart from `<%= @form.to_html %>` or similar.

—*Friday 25th June 2021.*

[ft]: https://www.crossingtheruby.com/2021/06/24/basic-form-formtastic.html
