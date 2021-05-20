---
layout: post
title: "Form Formation: Phoenix"
---

Elixir’s Phoenix grew out of the fertile soil (rose from the ashes?) of Ruby on Rails. It shares many conventions and approaches. You could say the mindset was “if it ain’t broke, don’t fix it”.

EEX, Elixir’s templating language, is almost identical, conceptually, to Ruby’s ERB. Phoenix’s templates and the HTML helpers also draw their inspiration from their Rails counterparts.

Take `form_for` and the form helper methods. Given a form variable `f` and a label for the `address` attribute:

```
# Rails

<%= f.label :address, class: "control-label" %>

# Phoenix

<%= label f, :address, class: "control-label" %>

```

The only difference is the natural and predictable switching of the form object, `f`, from receiving the `label` message (in Ruby), to being the first argument in the `label` function (in Elixir). This is consistent with Elixir’s functional style which has only modules and data structures and no classes or objects. That key structural difference aside, they are identical in form and function.

What José Valim did (that Rails did not) was write a definitive post about how to use the Phoenix primitives together with the “form object”—a Changeset implementing the `Phoenix.HTML.FormData` protocol—to implement a concise declarative form.

Mr. Valim finishes with this:

```
<%= input f, :name %>
<%= input f, :address %>
<%= input f, :date_of_birth %>
```

You can read the full post on the Dashbit blog: [Dynamic forms with Phoenix][dfp].

This is certainly more than half-way towards the fully declarative form I am searching for.

—*Thursday 20th May 2021.*

[dfp]: https://dashbit.co/blog/dynamic-forms-with-phoenix
