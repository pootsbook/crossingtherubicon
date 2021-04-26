---
layout: post
title: "Form Formation: Imperative vs. Declarative"
---

When writing about form formation from both [MVC][mvc] and [stateless function][sf] perspectives it occurred to me that this is also an imperative-declarative split.

The MVC solution is an imperative solution. The view is pieced together as an individual component and hooked up to the other parts manually.

The stateless function approach is more declarative, and once the connection between form object and UI is defined then the UI knows in effect how to draw itself.

This represents a certain inversion of control. In an imperative solution (which is also the traditional and most common approach) the view is responsible for gathering all of the disparate form elements together and mapping them to values on the form object. This is normally done with a set of form helpers, or straight form object value interpolation in the HTML.

In a declarative solution this mapping is defined up front and what gets rendered is dependent on a variant of a form object which contains that mapping inside it. In a way the form object then knows how to render itself and all of the work as to what a form will look like in terms of HTML has been defined up front.

I’d like to examine some of the trade-offs that both of these approaches bring in future posts.

—*Monday 26th April 2021.*

[mvc]: https://www.crossingtheruby.com/2021/04/24/form-formation-model-view-controller.html
[sf]: https://www.crossingtheruby.com/2021/04/25/form-formation-stateless-function.html
