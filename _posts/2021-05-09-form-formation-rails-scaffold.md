---
layout: post
title: "Form Formation: Rails Scaffold"
---

Yesterday I observed how [Padrino][pad] generates a static admin interface by using templates. This is similar to Rails’s scaffold generator.

This is based on a templating approach, where Rails provides a default template for a form out of the box. If you want to customise the scaffold that is possible by providing your own template. Once the scaffold has been generated it is a static artefact that must be modified by hand if updates or changes are desired.

This is what the default [form scaffold template][fst] looks like:

```
<%%= form_with(model: <%= model_resource_name %>, local: true) do |form| %>
  <%% if <%= singular_table_name %>.errors.any? %>
    <div id="error_explanation">
      <h2><%%= pluralize(<%= singular_table_name %>.errors.count, "error") %> prohibited this <%= singular_table_name %> from being saved:</h2>

      <ul>
      <%% <%= singular_table_name %>.errors.full_messages.each do |message| %>
        <li><%%= message %></li>
      <%% end %>
      </ul>
    </div>
  <%% end %>

<% attributes.each do |attribute| -%>
  <div class="field">
<% if attribute.password_digest? -%>
    <%%= form.label :password %>
    <%%= form.password_field :password, id: :<%= field_id(:password) %> %>
  </div>

  <div class="field">
    <%%= form.label :password_confirmation %>
    <%%= form.password_field :password_confirmation, id: :<%= field_id(:password_confirmation) %> %>
<% else -%>
    <%%= form.label :<%= attribute.column_name %> %>
    <%%= form.<%= attribute.field_type %> :<%= attribute.column_name %>, id: :<%= field_id(attribute.column_name) %> %>
<% end -%>
  </div>

<% end -%>
  <div class="actions">
    <%%= form.submit %>
  </div>
<%% end %>
```

It’s fairly standard. A form tag targeted at the given model with validation error messages at the top then a rendering of the controls arrived by enumerating the model’s attributes, a field per attribute. (One particular edge-case is that a field named `password` will get two controls, one for the password and another for a password confirmation.) At the end there is a submit button.

In a future post we can examine Rail’s builder strategy in more detail.

—*Sunday 9th May 2021.*

[pad]: https://www.crossingtheruby.com/2021/05/08/form-formation-padrino.html
[fst]: https://github.com/rails/rails/blob/main/railties/lib/rails/generators/erb/scaffold/templates/_form.html.erb.tt
