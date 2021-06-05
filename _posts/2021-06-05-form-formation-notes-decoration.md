---
layout: post
title: "Form Formation: Notes—Decoration"
---

Yesterday I published [Michel Marten’s notes][mmn] which make for a very lucid articulation of the form domain. I was taken away by their precision and concision, a distillation of the disparate themes that have been appearing in my research—themes that are all connected by their relationship to the handling of forms.

I’d like to revisit certain themes from Mr. Marten’s perspective. In this post I want to look at _decoration_. At the very core we have a simple data structure, the hash in Ruby, a struct or map in Elixir, a dictionary in Python. A structure with keys and values, attributes and variables.

With ActiveRecord this structure is hidden beneath the surface, we never see it in it’s simplest form. The hash is derived from the introspection of the related database table. In the case where we are simply using ActiveModel, the hash is passed into a constructor. Both of these scenarios _decorate_ the hash with extra functionality. For example they turn this hash into a Ruby object with accessors for reading and writing that are mapped from the key (attribute) and value (data).

What I love about this is how Mr. Marten’s has removed my Rails sunglasses to observe what is happening in the cold light of day. This is one bug bear about frameworks, they often obscure the simple, underlying design principles by teaching you a new vocabulary. (To be fair, this is also one of the great strengths of frameworks, the naming of a concept at a higher, framework-specific level, allowing you to think at a higher, more abstract level.) 

When you stare at an ActiveModel object hard enough, you realise that it is simply a decorator, it decorates the simple data structure with extra functionality. Specifically, it decorates the data with several methods (interfaces) that allow it to slot seamlessly into Rails’ Action Pack and Action View. It also decorates the data with validations. It is possible to decorate each attribute with any number of different validations, as well as validations on various combinations of attributes.

Scrivener also operates in very similar fashion but with a much simpler implementation. A Scrivener object wraps or decorates a hash, allows the provision of explicit accessors, and enables the object to be decorated with validations. As it is not, like ActiveRecord, strictly tied to a model or database object, Scrivener allows for further manipulation of the data before passing it on to another step in the overall flow.

In Ecto, a changeset is even more obviously a decorator. The original struct is but one value in a larger struct which also contains the validations.

Decoration is a very useful lens with which to view the object itself together with the validation element of the form’s data object.

—*Saturday 5th June 2021.*

[mmn]: https://www.crossingtheruby.com/2021/06/04/form-formation-notes.html
