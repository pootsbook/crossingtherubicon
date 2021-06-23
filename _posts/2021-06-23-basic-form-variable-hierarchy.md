---
layout: post
title: "Basic Form: Variable Hierarchy"
---

Categorising the variables in [yesterday’s post][yp] was helpful in noticing very concretely some of the themes in [Michel Marten’s notes][mmn].

If we try and put the variable elements into some kind of hierarchy then we start see the decorating structures more clearly. 

Limiting ourselves to the elements of a field in the first instance, we can start from a central core, the data structure.

```
email_subscription = {
  email: "address@example.com"
}
```

This gives us the attribute name `email` and the value `address@example.com`. Let’s also take the variable name as the object name `email_subscription`.

This is our core. From this we can derive everything in the _“object” name + attribute name_ category, as well as the _object attribute_ category.

We can decorate this with the form-control type which is `input[type=email]`.

We can then also further decorate this with _validations_ and the associated _error messages_.

The last element of decoration is the _UI Text_ including the label and the help text. 

That covers the outward decoration from the form/model object and gives us everything we need for that particular field.

The form’s action and method are routing concerns, and the button text a UI concern. These are distinct from the field itself. In a framework like Rails, building on convention, the action and method are derived from the model object. Elsewhere these can be defined explicitly.

It would also be interesting to compare where in a framework or library each of these individual elements has their source. Are these sources consistent across libraries or are they mixed up?

—*Wednesday 23rd June 2021.*

[yp]: https://www.crossingtheruby.com/2021/06/22/basic-form-variable-categories.html
[mmn]: https://www.crossingtheruby.com/2021/06/04/form-formation-notes.html
