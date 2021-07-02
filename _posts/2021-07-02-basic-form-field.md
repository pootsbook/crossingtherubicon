---
layout: post
title: "Basic Form: Field"
---

Although I started with a rough design for a [widget][w] let’s define a field to act as a foundation. We listed and categorised the necessary attributes in [variable categories][vc] and the relevant list is the last one that categorises by data source.

That means we need the object name, the attribute name, the attribute value and type, and all of the user-facing text comprising of label, help and errors.

There is a choice here to also pass in a more global form object like Rails does by default, or only pass in the necessary information that the field needs. We’ll go with the latter for now.

A rough sketch:

```
class Field
  ATTRS = %i(
    errors
    help
    label
    name
    object_name
    type
    value
  )
  attr_accessor *ATTRS
  
  def initialize(attrs)
    ATTRS.each do |attr|
      instance_variable_set("@#{attr}", attrs.dig(attr))
    end
  end
end
```

This would be called in the following way, assuming our [ActiveModel EmailSubscription object][ams] instantiated as `@form`:

```
Field.new({
  errors: @form.errors[:email],
  help: "We'll never share your email with anyone else.",
  label: "Email address",
  name: :email,
  object_name: :subscription,
  type: :email,
  value: @form.email
})
```

We could of course later figure out a way to make the passing of these details a little less repetitive.

Given this field object we can extend the class with a few methods to generate the necessary elements before rendering it.

—*Friday 2nd July 2021.*

[w]: https://www.crossingtheruby.com/2021/07/01/basic-form-widget.html
[vc]: https://www.crossingtheruby.com/2021/06/23/basic-form-variable-hierarchy.html
[ams]: https://www.crossingtheruby.com/2021/06/17/basic-form-rails.html
