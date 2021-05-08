---
layout: post
title: "Form Formation: Padrino"
---

It occurred to me after having written about [Django forms][df] that the Django admin interface was the inspiration for a small Ruby framework built on top of Sinatra.

[Padrino][pad], (the “godfather of Sinatra”) boasted an automatic admin interface for your application models and associated database tables.

So I dusted off the Padrino codebase (I think I came across it in 2010)—well, dusting it off is a bit unfair, it seems to be quite current and going strong.

The main question I wanted to answer was how it arrived at the forms in the admin interface? Are they dynamically generated like Django or are they statically generated once like Rails’ scaffolds?

I was hoping for the former but it turns out that it is the latter.

Padrino operates on the same basis as Rails, using a generator to spit out the HTML code from a template which is dynamically populated by introspecting the application model. This is done once, and the template which gets generated needs to be edited by hand for any extensions.

Here is the [“edit” page template][ept] from Padrino’s generator:

```
<ul class="nav nav-tabs">
  <li> <%%= link_to tag_icon(:list, pat(:list)), url(:<%= @orm.name_plural %>, :index) %></li>
  <li> <%%= link_to tag_icon(:plus, pat(:new)), url(:<%= @orm.name_plural %>, :new) %></li>
  <li class="active"> <%%= link_to tag_icon(:edit, pat(:edit)), url(:<%= @orm.name_plural %>, :edit, :id => @<%= @orm.name_singular %>.id) %></li>
</ul>
<div class="tabs-content">
  <%% form_for :<%= @orm.name_singular %>, url(:<%= @orm.name_plural %>, :update, :id => @<%= @orm.name_singular %>.id), :method => :put, :class => 'form-horizontal' do |f| %>
    <%%= partial '<%= @orm.name_plural %>/form', :locals => { :f => f } %>
  <%% end %>
</div>
```

And the [“form” partial template][fpt]:

```
<% @orm.column_fields.each_with_index do |column, index| -%>
<%% error = <%= @orm.has_error(column[:name]) %> %>
<fieldset class='control-group <%%= error ? 'has-error' : ''%>'>
  <%%= f.label :<%= column[:name] %>, :class => 'control-label' %>
  <div class='controls'>
    <%%= f.<%= column[:field_type] %> :<%= column[:name] %>, :class => 'form-control input-xlarge input-with-feedback'<%- if (index == 0) -%>, :autofocus => true<%- end -%> %>
    <span class='help-inline'><%%= error ? f.error_message_on(:<%= column[:name] %>) : "Ex: a simple text" %></span>
  </div>
</fieldset>
<% end %>

<div class="form-actions">
  <%%= f.submit pat(:save), :class => 'btn btn-primary' %>
  &nbsp;
  <%%= f.submit pat(:save_and_continue), :class => 'btn btn-info', :name => 'save_and_continue' %>
  &nbsp;
  <%%= link_to pat(:cancel), url(:<%= @orm.name_plural %>, :index), :class => 'btn btn-default' %>
</div>
```

So you’re stuck with the form that you generated at the point of generation. It doesn’t stay up-to-date with changes to the model. Any HTML or model changes need to be executed by hand.

There is probably a way to modify the template that the generator uses, but this simply gives you more control over the eventual HTML and doesn’t save you from the static nature of the resulting template.

Django then seems to be operating at a level of abstraction above that of Rails and Padrino. And judging by the prevalence of Rails’ and Padrino’s approaches in the wider ecosystem of web frameworks the necessity for that extra level of abstraction is questionable.

—*Saturday 8th May 2021.*

[df]: https://www.crossingtheruby.com/2021/05/05/form-formation-django-forms.html
[pad]: https://padrinorb.com
[ept]: https://github.com/padrino/padrino-framework/blob/master/padrino-admin/lib/padrino-admin/generators/templates/erb/page/edit.erb.tt
[fpt]: https://github.com/padrino/padrino-framework/blob/master/padrino-admin/lib/padrino-admin/generators/templates/erb/page/_form.erb.tt
