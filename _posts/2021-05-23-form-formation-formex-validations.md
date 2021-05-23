---
layout: post
title: "Form Formation: Formex Validations"
---

Yesterday I had a look at how [Formex constructs a form][fmx] and generates the associated HTML. The eagle-eyed among you will have spotted that Formex provided a way to specify validations in its builder.

In this sense Formex attempts to be a ‘batteries-included’ solution and caters for different validation libraries via an adapter. It has a simple, in-built single validation validator which simply asserts that a given attribute is required. On top of that it has “glue” libraries for Ecto.Changeset validations and for the [Vex][vex] validation library.

Formex therefore envelops the form object, layering the form fields and validations on top of the object (or struct in Elixir’s case).

It provides an interface to Ecto via the [Formex Ecto][fe] library. The crucial function is `Formex.Ecto.Controller.insert_form_data/1` (alongside a corresponding update function). This abstracts some of the boilerplate away so that inserting data to the database from the form in the controller is fairly straightforward. It looks like Formex.Ecto is copying the changeset errors from the Ecto Changeset to the Formex Form struct behind the scenes on an unsuccessful validation.

It’s difficult for me to comment on the pros and cons of this approach having not used it in anger and not having a deep enough experience with Phoenix applications.

The approach is designed to work well with Ecto and to slot into the elements that already exist in a standard Phoenix application. It seems to add a layer of convenience for building more complex forms, removing the boilerplate configuration for things like associated models, and allowing a declarative approach to defining the form that will be generated and used.

What is interesting is that this demonstrates that just like in Python and Django, Ruby and Rails, there are declarative approaches to be found that depart from the normal imperative route. These, on balance, are not as popular as their imperative counterparts.

—*Sunday 23rd May 2021.*

[fmx]: https://www.crossingtheruby.com/2021/05/22/form-formation-formex.html
[vex]: https://github.com/CargoSense/vex
[fe]: https://github.com/jakub-zawislak/formex_ecto
