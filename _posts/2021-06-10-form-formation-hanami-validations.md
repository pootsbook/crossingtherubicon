---
layout: post
title: "Form Formation: Hanami Validations"
---

Validations in Hanami are accomplished by including the `Hanami::Validations` mixin to a Ruby object.

This gives Hanami an air of flexibility compared to the inheritance-based approach of ActiveRecord. Scrivener also includes validations as a mixin while offering an additional layer of convenience when inheriting from the `Scrivener` class.

`Hanami::Validations` offers all of the usual suspects in a validation library and more. From filtering out keys, required and optional keys, type safety, predicate based assertions to advanced usage with macros, rules (form level vs. field level validation), composition of validations for (deeply) nested data.

This is what a validator (with a nested schema/validator on `customer`) might look like:

```
class OrderValidator
  include Hanami::Validations

  validations do
    required(:code)
    required(:customer).schema(CustomerValidator)
  end
end
```

And this is how it would be run:

```
result = OrderValidator.new({}).validate
result.success? # => false
```

The `result` object can return error messages via `result.messages` and the resulting data (having been passed through the validator) via `result.output`.

Again we see this more functional style where we are not modifying the original object/data but decorating the that data instead.

In Hanami proper, and when working with forms, it has an extra ‘schema type’ which eases the handling of raw form data coming through as a convenience.

Hanami validations feel very similar in some ways to Ecto changesets. How one maps the data from a validator (e.g. errors) back to a form will be an interesting investigation.

—*Thursday 10th June 2021.*
