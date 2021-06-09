---
layout: post
title: "Form Formation: Hanami HTML"
---

Yesterday I offered a very brief view of how [Hanami deals with forms][hf], noting its similarity to both Rails and Hypertext.

The main benefit with Hanami’s approach over Rails with respect to HTML is that it has a cleaner approach to HTML generation for forms. There is no need for string concatenation between helper methods that are element siblings.

For example, in Rails, if I were to want a helper method to encapsulate both a form-control with the associated label (instead of declaring them separately in the template) then I have to concatenate the return values of both helper methods:

```
def text_field_with_label(attribute)
  label(attribute) + text_field(attribute)
end
```

The example is contrived and bare, but it illustrates the kind of dissonance switching between the template and application code. Hanami avoids that:

```
div do
  label :title
  text_field :title
end
```

I’m unsure at this stage how I would write my own helper to encapsulate this, but it looks possible by extending the built-in helpers and adding an HtmlNode to the `@nodes` array. (Of course that begs the question if an extra helper is necessary given the elegant HTML helper API.)

Similar to Rails, it automatically caters for the HTML attributes on inputs: `id`, `name` and `value` and allows you to override them as you see fit. 

Nested fields and nested collections are also possible using the `fields_for` and `fields_for_collection` methods.

One of the more interesting aspects is that you pass the data / model to the form explicitly via the `:values` option to `form_for`. This maps the form attributes with the values automatically. What happens implicitly is that the parameters are also passed in, allowing these to take precedence over values which means that on unsuccessful form submissions the form will correctly prefer the edited values stored in params over the values originally passed in.

This is a nice trick to decouple the original data being passed in to the form, alongside the changes that have been made by the user. There is no need therefore for a model object to maintain its own state. The original and the changes are kept apart, similar to how an Ecto Changeset stores the original and the changes separately.

This also makes it ORM agnostic, meaning it can be used with any ORM or none. 

Without a set model object, it will be interesting to see how Hanami handles validations.

—*Wednesday 9th June 2021.*

[hf]: https://www.crossingtheruby.com/2021/06/08/form-formation-hanami.html
