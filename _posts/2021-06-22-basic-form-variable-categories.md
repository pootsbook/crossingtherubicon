---
layout: post
title: "Basic Form: Variable Categories"
---

[Yesterday][yt] I listed out all of the variable elements of our basic form. If I try and categorise these elements I come up with the following:

- form
  - `action`
  - `method`
  - button text
- field
  - `label.for`
  - `label.text`
  - `input.type`
  - `input.name`
  - `input.id`
  - `input.class[is-invalid]`
  - `input.aria-describedby`
  - `input.value`
  - error text
  - help `id`
  - help text

If I try and group them by concern I come up with the following:

- Routing
  - `form.action`
  - `form.method`
- References
  - `field.label.for`
  - `field.input.name`
  - `field.input.id`
  - `field.input.aria-describedby`
  - `field.help.id`
- Validations/Errors
  - `input.class[is-invalid]`
  - `field.error.text`
- User Communication
  - `form.button.text`
  - `field.label.text`
  - `field.help.text`
  - (`field.error.text`)
  - `field.input.value`
- Miscellaneous
  - `field.input.type`

If I think about it in relation to the source of the information that populates the variables:

- form/routing concerns (object?)
  - `form.action`
  - `form.method`
- “object” name + attribute name
  - `field.input.id` (`#{object}_#{attribute}`)
    - `field.label.for`
  - `field.input.name` (`#{object}[#{attribute}]`)
  - `field.help.id` (`#{object}_#{attribute}_help`)
    - `field.input.aria-describedby`
- “object”
  - attribute
    - `field.input.value`
    - `field.input.type` *
- UI Text
  - form
    - `button.text`
  - field
    - `field.label.text`
    - `field.help.text`
    - errors
      - `field.error.text`
- Validations/Errors
  - `field.input.class[is-invalid]`
  - (`field.error.text`)

\* Not sure if this is the correct place for `field.input.type`. It could be an imperative mapping in the template, it could be a declarative mapping in a form object, or it could be a dynamic calculation from the form/model object’s data type.

I’m not sure what all of this means, but it’s helpful to trace the dependencies, the source, and the category of each variable element in the basic form.

—*Tuesday 22nd June 2021.*

[yt]: https://www.crossingtheruby.com/2021/06/21/basic-form-variables.html
