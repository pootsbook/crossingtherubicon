---
layout: post
title: "Form Formation: Rails Form Builder"
---

To construct its HTML forms Rails uses the builder pattern to link a form object (ActiveRecord or ActiveModel object) to an HTML form via helpers.

The builder pattern is a classic object oriented design pattern that avoids a given class (in this case ActiveRecord/Model) needing to encapsulate the complexity of creating different representations (in this case, forms) of its objects.

So there is an inherent connection between the object (the form object) and the builder class itself. The builder class then takes on the complexity of building out a representation (the form HTML). 

There is method in the madness. The ActiveRecord class is filled with a large amount of complexity already, and forms are a “view” concern, therefore it makes sense to separate the two.

The class that Rails provides is [`ActionView::Helpers::FormBuilder`][avhfb]. It takes an ActiveRecord object via `form_for` and then yields the builder object, allowing you to call helper methods on the builder object that automatically connect a form control with the corresponding attribute on the ActiveRecord object.

An example from the documentation:

```
<%= form_for @person do |form_builder| %>

  <div class="form-control">
    <%= form_builder.label :name %>
    <%= form_builder.text_field :name %>
  </div>
  
  <div class="form-control">
    <%= form_builder.label :admin, "Admin?" %>
    <%= form_builder.check_box :admin %>
  </div>
  
<% end %>
```

So Rails gives you several conveniences. First of all, it can introspect the model object to automatically figure out the form `action` URL (assuming conventions are being followed) and the form `method` (it will almost always be `POST` but Rails adds a hidden field on the form to indicate `PUT`/`PATCH` for models that have already been persisted).

Secondly, it links up HTML form controls with their corresponding attributes, providing sensible defaults for the text node inside a `label`, the correct value for its `for` attribute, the `id`, `name` and `value` of the form control.

These are nice conveniences, but the specification of the link between attribute and form control remains a manual configuration in the ERB template, and must be reproduced each time you declare a new form.

They are conveniences to make the writing of HTML easier within a template, rather than to remove the need to write HTML by abstracting into a “widget” like Django/Bureaucrat.

—*Monday 10th May 2021.*

[avhfb]: https://api.rubyonrails.org/classes/ActionView/Helpers/FormBuilder.html
