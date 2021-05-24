---
layout: post
title: "Form Formation: Bootstrap Form"
---

Upon reading some of my posts about form formation in Ruby a colleague pointed out the [Bootstrap Form][bf] Ruby gem.

This gem is firmly in the camp of HTML generation. It is follows the conventional Rails approach of defining a custom form builder, inheriting from the in-built default. I examined the writing of a custom form builder in [Rails ViewComponents][rvc] after an initial look in [Rails Form Builder][rfb].

What Bootstrap Form does is provide out-of-the-box Bootstrap markup for forms. By using the method `bootstrap_form_for` instead of the default `form_for` and simply using a one-line statement per field it produces the fully fledged Bootstrap markup including label, form control and error messages.

```
<%= bootstrap_form_for(@user) do |f| %>
  <%= f.email_field :email %>
  <%= f.password_field :password %>
  <%= f.check_box :remember_me %>
  <%= f.submit "Log In" %>
<% end %>
```

This looks remarkably like the [dynamic Phoenix forms][dpf]. It is concise, and it is a clear mapping between attribute and form control. It is technically imperative, but carries a declarative feel. The form builder takes all of the responsibilities for generating the field, almost acting like the widget in the more declarative approaches of [Django][dj] and [Bureaucrat][bu]. The main distinction is that here the mapping is happening in the template itself.

This opens up another axis of enquiry. Given HTML forms can be so complex (see [persisting drafts][pd]) and that many of the conventions live in some particular configuration of the HTML, there is a great benefit in unifying this and componentizing it. In this case the componentization of Bootstrap markup is happening in the custom form builder, nicely abstracted away from view.

—*Monday 24 May 2021.*

[bf]: https://github.com/bootstrap-ruby/bootstrap_form
[rvc]: https://www.crossingtheruby.com/2021/05/15/form-formation-rails-view-components.html
[rfb]: https://www.crossingtheruby.com/2021/05/10/form-formation-rails-form-builder.html
[dpf]: https://www.crossingtheruby.com/2021/05/20/form-formation-phoenix.html
[dj]: https://www.crossingtheruby.com/2021/05/05/form-formation-django-forms.html
[bu]: https://www.crossingtheruby.com/2021/05/02/form-formation-bureaucrat.html
[pd]: https://www.crossingtheruby.com/2021/05/14/form-automation-benefit-persisting-unfinished-forms-programmatically.html
