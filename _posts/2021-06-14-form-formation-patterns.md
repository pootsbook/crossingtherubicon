---
layout: post
title: "Form Formation: Patterns"
---

While the research has not been exhaustive I have covered a considerable majority of popular form libraries written in Ruby. Some tied to a framework like Rails, some a part of other frameworks, and others stand-alone.

Without a doubt, the most common pattern that has come up time and again is the form builder pattern. The form builder is an object representing the HTML form that takes a form or model object. 

This form builder then exposes a set of helper methods that take an attribute as an argument. This imperative declaration of the attribute is a means of mapping the associated helper to a given attribute of the model object. This allows the helper to look up relevant properties on the model object for configuring elements and/or attributes of the HTML.

In Rails if you want different HTML you write your own helper methods. In Forme you can hook into the various transformers.

All of the Rails libraries, the default form builder, Bootstrap Form, Formtastic, Simple Form operate on this principle. Forme works without the model object but seems to be most useful when it does, taking advantage of the conventions. Forme builds in more customization so that you can likely avoid writing your own helpers.

Reform (Trailblazer) and YAAF (Rails) both focus on the creation of form/model objects. Reform is decoupled from a given model, much like Hanami validations.

These last two are similar to Ecto Changesets in that a backing model object is not required but it pulls all of the necessary elements together with a common interface.

Bureaucrat and Django then represent a different approach with the HTML being declaratively rendered from a model object where the configuration lives outside of the HTML and in a separate object.

I need to think some more about the life cycle, the individual parts, and the ways in which the parts are put together.

—*Monday 14th June 2021.*
