---
layout: post
title: "Form Formation: Model Object"
---

I detailed four [constituent parts][cpf] to the construction of a (data-backed) form:

1. The HTML (and eventual helpers)
2. The [styling][fhs] (a variant of the HTML)
3. The data object (collection of key-value pairs)
4. The life cycle meta-data (validation, error messages)

In this post I want to consider the data object. This can be as simple as a collection of key-value pairs, e.g. a hash (in Ruby), and as complex as a fully-fledged model object, e.g. an ActiveRecord model instance (in Rails).

For the purposes of the form it really is that simple. The form requires a set of keys which will translate to form fields and their associated values which will translate to the `value` attribute on the form control or it’s text node (e.g. `textarea`).

A Scrivener object has an `#attributes` method which translates the object attributes to a hash which can then be inserted into the HTML. ActiveRecord has an identically named method doing pretty much the same thing.

The simplest “model object” is then a hash or dictionary. For a registration form:

```
{
  name: "",
  email: "",
  password: ""
}
```

All three elements up until now, HTML, styling (which is really just a variant of HTML), and the model object are in themselves very simple. The complexity then must lie in the combination of these elements together to get the desired result, and potentially also in the fourth element, the life cycle. We’ll explore that next.

—*Wednesday 21st April 2021.*

[cpf]: https://www.crossingtheruby.com/2021/04/19/form-formation-constituent-parts.html
[fhs]: https://www.crossingtheruby.com/2021/04/20/form-formation-html-form-styling.html
