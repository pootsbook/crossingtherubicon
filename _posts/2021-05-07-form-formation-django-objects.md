---
layout: post
title: "Form Formation: Django Objects"
---

When looking into [Django Forms][df] in a previous post, it became clear that Django operates differently to most other solutions to forms in popular libraries and frameworks.

Django does something similar to what I observed with [operations as data][oad]. In operations as data, a given operation is encoded in/as a data structure and then passed on to a function to be executed, neatly describing the operation. The alternative is simply to execute the operation without this intermediate encoding.

With Django, it zooms in on a form at a much more granular level than other approaches. It makes the form itself a class, and it makes each field a class. The field is associated with a widget, another class, and any validation errors are encoded as a data structure on the form class.

In other approaches there is the form or model object, an object describing the data related to the form, and there is an object or data structure for the validations and/or error messages. But that’s where it ends. The form and the fields and the associated HTML generation are all up to you.

In Django, you simply have to print a form. Given a (Django) form object, `f`, it’s as straightforward as `print(f)` and the HTML is generated with all of the necessary fields, associated values and their error messages.

There is a flexibility involved with common CSS classes for errors and required fields. Widgets can be modified or even swapped out completely for custom controls. If labels need to be customised or all `name` attributes need a prefix, this is all possible.

An interesting data point to gather would be to find developers who have either migrated away from Python/Django to another language/framework combination, or in the opposite direction, and to ask them how their experiences of working with forms differed.

Without having used this approach in anger it’s difficult to arrive at anything more than a superficial opinion. Nevertheless, this seems very close to the direction I had in mind.

—*Friday 7th May 2021.*

[df]: https://www.crossingtheruby.com/2021/05/05/form-formation-django-forms.html
[oad]: https://www.crossingtheruby.com/2021/01/17/operations-as-data.html
