---
layout: post
title: "Form Formation: Life Cycle"
---

The last, and potentially the most complex piece of the form puzzle is the form’s life cycle. I’m using this term to describe the meta-data that is bundled with the form’s model object that extends form’s existence across multiple submissions or round trips to the server.

The life cycle is the glue that holds together the validations, the error messages and any other meta-data required to present a form submission in a certain state across time.

Validations and their associated data, like error messages, certainly present the most common use-case here. Another potential piece of meta-data that might be taken into account is whether or not the model object is persisted or not, thus modifying the form’s method or destination URL as a result. For now we’ll focus on the validation or error data.

Rails’s ActiveRecord is a batteries included library. Not only batteries but also the kitchen sink. The model object is decorated with any number of meta-data and validation errors are one such datum. Calling `#valid?` on an object will populate the `#errors` attribute. This returns an `ActiveModel::Errors` object with a collection of error types and messages associated with a given model attribute.

Scrivener is an example of a simple decorator which wraps a primitive Hash of attributes with validations and provides a simple errors hash, with an attribute’s symbol used as a hash key pointing to an array of associated errors.

In Ecto, a changeset has a list of errors which is keyword list where the key is an atom corresponding to the attribute and the value is itself a two tuple with an error message as the first element and extra options as the last element.

Now this doesn’t actually seem that complex either. It is similar to the model object we saw [yesterday][fmo]. So the real complexity must come from linking all of these things up. There are a number of assumptions or conventions that must be respected to get things right.

There must be a reference to the model object, there must be a reference to the validation object, and there must be a link between a validation or error for a given attribute and the corresponding attribute on the model object.

That seems to be it. In future I’ll dive into how all of these parts are hooked up as well as make some general observations before diving into particular implementations.

—*Thursday 22nd April 2021.*

[fmo]: https://www.crossingtheruby.com/2021/04/21/form-formation-model-object.html
