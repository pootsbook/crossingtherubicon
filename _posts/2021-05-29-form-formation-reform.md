---
layout: post
title: "Form Formation: Reform"
---

[Reform][rf] is the framework-agnostic form library for Ruby applications. It is part of [Trailblazer][tb] the advanced framework-agnostic framework (I know, right?) for business logic with Ruby.

Trailblazer is interesting for all manner of reasons but I’m going to do my best to limit myself to looking at Reform, which can be used independently of the rest of Trailblazer.

Indeed, it has an associated `reform-rails` Rubygem for usage within Rails, but is equally at home in any other framework.

Within Rails it is designed to work seamlessly together with the in-built form builder, and even compatible with [Formtastic][ft] and [Simple Form][sf].

This gives us the first indication that Reform is a different kettle of fish. The focus of Reform is not on the rendering of the HTML but in the management of the data that is being instantiated or manipulated via an HTML web form.

It is then, in the truest sense (or just my sense, if you will), a form library, because it creates an abstraction for the form itself, abstracting the form’s object (data structure, model) and the associated validations. It also means you can create a form without being backed by a model. 

In some ways it resembles one half of [Scrivener][scrv] (the form class/object) and offers a means of plugging in the second half (validations).

The agnostic approach is thus evident throughout the library; you can plug-in your own validation back-end (ActiveModel::Validations, dry-validation), you can use your own rendering library (Rails default, Formtastic, Simple Form); you can place it in any framework.

So if it’s handing off all of these responsibilities to others what does it do itself? 

It acts as another layer slotted in between the form rendering and the model object. It allows you to define a form as its own entity, making this explicit. This layer acts as a mechanism to decouple the one-to-one mapping of a model to a form and opening up different possibilities:

- a form without a model backing it
- a form that works across multiple model objects
- a form that works with (multiple) nested model objects

One subtle but important distinction is that it also lifts the validations from the model layer to the form layer, handling the data before it gets persisted.

There’s a lot more to it than what’s here at first glance as can be expected from an _advanced_ business logic framework. It certainly looks comprehensive. More details can be found in the [Reform documentation][rfd].

—*Saturday 29th May 2021.*

[rf]: https://github.com/trailblazer/reform
[tb]: https://trailblazer.to/2.1/
[ft]: https://www.crossingtheruby.com/2021/05/27/form-formation-formtastic-rails.html
[sf]: https://www.crossingtheruby.com/2021/05/28/form-formation-simple-form-rails.html
[scrv]: https://www.crossingtheruby.com/2021/02/25/validations-with-scrivener.html
[rfd]: http://trailblazer.to/2.1/docs/reform.html
