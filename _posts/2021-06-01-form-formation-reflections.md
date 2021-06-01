---
layout: post
title: "Form Formation: Reflections"
---

So far in my research into form formation I have come across several themes that I want to jot down at this stage in the process.

- Forms are a knotty problem. They touch on a variety of concerns within a web application.
- Many responsibilities are clumped together for convenience and it seems difficult to separate them.
- This leads to a number of different approaches which tackle a certain subset of the set of all related responsibilities.
- The other elements in the given framework or approach greatly dictate how forms are dealt with.
- Forms seem to be an afterthought, with other aspects of the framework taking priority.
- It seems too difficult to arrive at a form-centric approach given that putting forms at the centre would compromise the other aspects.

The responsibilities related to working with forms fall into the following buckets:

- HTML generation
  - tag/attribute customisation
    - wrappers
    - classes
    - structure
  - form-control to ‘model’ attribute mapping
    - values
    - type
    - errors
- Form Object
  - object or data structure representing the contents of the form
    - one-to-one with a model object
    - non-model backed data
  - advanced scenarios with nested models, multiple models, model subsets
- Validations
  - attached to the form object or
  - attached to the model object
  - handling and display of errors (linked to HTML generation)

HTML generation is very dependent on the templating library, and more often than not it is completely separated from any logical representation of the form.

Outliers are libraries/frameworks that make the HTML a direct expression of an independent form representation.

This form representation could also be called a form object, that is, a dedicated structure that is not the data (the _object_ that the _form_ represents) but an _object_ that is a representation of the _form_. This is quite confusing because we have one term to describe two very distinct things. Even this distinction is clouded by many implementations.

What gives me reason for encouragement is that not everyone is satisfied with the tools available. A quote from one of my readers:

> There's no form object gem or design pattern that I'm super happy with in Rails

The question of course is, are the tools that _are_ available good enough?

—*Tuesday 1st June 2021.*
