---
layout: post
title: "Form Formation: Model View Controller"
---

When looking at the constituent parts of putting together a web form it occured to me that it follows the basic properties of the traditional Model View Controller web architecture.

The view is the HTML and CSS which mixes in data from from the model object and the life cycle.

The model is the model object itself, the representation of a domain model in a simple data structure.

The controller is the life cycle or the validation process. It controls whether or not the model object is in a state to proceed to the next point in the application or business process.

In this way the actual form submission event would be analogous to a controller action.

It is a self perpetuating component, a closed system. Until the model object is valid according to the rules the form will continue to be presented, complete with error messages. When a model object is valid then we exit that system, passing the data on to wherever it needs to go.

Viewed from this vantage point a form is a miniature MVC component.

—*Saturday 24th April 2021.*

[cpf]: https://www.crossingtheruby.com/2021/04/23/form-formation-patchwork.html
