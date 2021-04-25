---
layout: post
title: "Form Formation: Stateless Function"
---

In [yesterday’s post][mvc] I imagined a form to resemble a miniature (back-end) MVC component. When the Model View Controller pattern moved from the back-end to the front-end it didn’t last very long in that particular form. 

It was replaced, thanks in many ways to React, with the view being nothing other than a function of application state. That is, the view was a stateless function into which was passed the state of the application, and from which the application was rendered.

While popularised by React, the constrained programming environment of languages like Elm left no other option but to design an application in this way. When state is immutable then a view becomes a stateless (or pure) function.

If we apply this approach to our form problem, viewing it from this particular angle, then we can see the form as nothing other than a data structure (state) that is subsequently passed to a stateless function for rendering.

Our stateless function will output the required HTML and CSS. So a form helper in this approach is a function whose output is predicated on the form state (input). 

```
helper(form) #=> HTML
```

If this is the case then we need a data structure that caters to all of the possible configurations that a form might take. We can attack that problem in two ways. Either get a list of all possible outputs and work backwards to the inputs, or get a list of all possible inputs and work forwards to the outputs.

Again, it is a closed loop. A well defined problem.

It’s interesting to look at the form from different angles, first MVC, then as a stateless function.

—*Sunday 25th April 2021.*

[mvc]: https://www.crossingtheruby.com/2021/04/24/form-formation-model-view-controller.html
