---
layout: post
title: "Form Formation: Formtastic (Rails)"
---

[Formtastic][ft] is one of the most popular Rails form libraries. Here is how it describes itself:

> Formtastic is a Rails FormBuilder DSL (with some other goodies) to make it far easier to create beautiful, semantically rich, syntactically awesome, readily stylable and wonderfully accessible HTML forms in your Rails applications.

Formtastic is a domain specific language (DSL) for building forms in Rails, extending the in-built form builder by inheriting from `ActionView::Helpers::FormBuilder`.

This means that in terms of how it generates the HTML, it is, in principle, the same as what is already in Rails.

Where it differs, and adds value, is in offering a more ergonomic API than that which Rails provides out of the box. Additionally it offers a set of configuration options that Rails doesn’t provide which prevents you from re-inventing the wheel, configuring settings for all forms across your application in one place.

The most exciting thing I found, was that although the generation of form controls and mappings is in effect identical to Rails in an imperative style, it also supports a declarative way of rendering the whole form in one go.

```
<%= semantic_form_for @user do |f| %>
  <%= f.inputs %>
  <%= f.actions %>
<% end %>
```

That’s pretty neat, and not something I was aware of. It does come with the proviso:

> This is a great way to get something up fast, but like scaffolding, it's not recommended for production. Don't be so lazy!

I’m lazy. Aren’t good software developers supposed to be lazy? On a more serious note, it operates under the assumption that something you can generate declaratively is unfit for production use. 

I can’t quite work out if this an assumption similar to the one many people make about Redis—“oh, but you can’t use Redis as your primary database” when, in actual fact, you can—or if there is more substance behind this statement that I will have to find out about the hard way.

It’s difficult for me to get over the fact that such a Django-like declarative rendering of forms was happening right under my nose in the Rails world. Who knew?

—*Thursday 27 May 2020.*

[ft]: https://github.com/formtastic/formtastic
