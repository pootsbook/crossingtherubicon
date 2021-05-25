---
layout: post
title: "Form Formation: Django vs. Rails"
---

Who doesn’t love a good battle? Django vs. Rails is the archetypal framework war. Back then you wouldn’t have gone wrong choosing either one. Django and Rails are both going strong, arriving at a level of maturity that lifts them out of these kinds of debates.

When it comes to form handling my bias and proclivity for Ruby and Rails makes me the most unsuited of arbiters. So I went searching for opinions about the different approaches to forms in both frameworks.

Perhaps it is my lack of search engine fu, or perhaps the simple fact that if you have settled on one framework you have no reason to investigate the other, but there was a distinct paucity of strong opinions on comparing approaches to forms in both frameworks.

Of course it may be another indication that this isn’t such a big deal in the grander scheme of things but for now I’ll avoid the argument from silence.

This comparison, [Forms—Comparing Django to Rails][cdr], ends up with many of the same observations from my [superficial examination][se]. Here are the highlights (headings original, emphasis mine):

> ## Django has built-in form objects

> Rails makes no distinction between models and forms, at least in the controller. Model objects are used to render forms, and controllers pass user input directly into model objects for validation.

> Many Rails projects make use of form objects, but Rails does not provide them out of the box. ActiveModel::Model can be used to make form objects fairly easily, but still, **forms are not a first-class citizen**.

> ## Django forms can take fields from models

> A common complaint about Rails form objects is that they duplicate validation logic that already exists in models.

> Django forms take their fields and validations from models objects, limiting duplication. They are not limited to the fields in the model, though. They can take a subset of the model fields, add extra fields of their own, and replace model fields with different ones.

> ## Django forms render themselves

> Django form objects can render themselves into HTML. The form’s field objects have various different options which control how they are rendered.

> Rails takes a more explicit approach, using FormBuilders to generate HTML based on model objects. Automatic form rendering wouldn’t be feasible without control over which fields to render, at which point you basically have form objects.

> ## Django forms contain business logic

> With the ability to render themselves, this makes Django forms a strange blend of model, view and business logic responsibilities. Put another way: **they are complicated.**

(Related: see my [MVC observation][mvco] about forms)

Although one might dismiss Django on the basis of these comments, it’s good to think about whether this is a reaction to being presented with different code “buckets” matching a different mental model to the one formed by Rails.

There is enough to suggest that a distinct form object would introduce some decoupling within Rails from the direct form-model correspondence found there.

And what’s not to like about this Django form rendering code translated to Ruby:

```
<%= form_for(@form) do %>
  <%= @form %>
<% end %>
```

—*Tuesday 25th May 2021.*

[cdr]: https://www.rubypigeon.com/posts/forms-comparing-django-to-rails/
[se]: https://www.crossingtheruby.com/2021/05/05/form-formation-django-forms.html
[mvco]: https://www.crossingtheruby.com/2021/04/24/form-formation-model-view-controller.html
