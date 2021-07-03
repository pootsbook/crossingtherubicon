---
layout: post
title: "Basic Form: Field Decoration"
---

[Yesterday][bff] we created a `Field` class that contained all of the source data necessary for building out a field in HTML.

Today we can look at how to decorate this data with some helper methods to calculate the necessary derivatives.

Our categorised and sorted [list of data][lod] defines the following derived elements coexisting alongside the original data:

- `input[id]` and `label[for]`
- `input[name]`
- `help[id]` and `input[aria-describedby]`

Let’s add some methods to our field class:

```
class Field
  …
  def input_id
    [object_name, name].join("_")
  end
  
  def input_name
    "#{object_name}[#{name}]"
  end
  
  def help_id
    [input_id, "help"].join("_")
  end
end
```

This is all we need. We could memoize the `input_id` to ensure it isn’t being calculated from scratch each time. This way we ensure that it remains identical (an edge-case but it feels safer given the values need to be identical for the label and input to correspond properly in the HTML).

```
class Field
  …
  def input_id
    @input_id ||= [object_name, name].join("_")
  end
  …
end
```

I believe this is everything we would need for our basic field before rendering it to HTML.

—*Saturday 3rd July 2021.*

[bff]: https://www.crossingtheruby.com/2021/07/02/basic-form-field.html
[lod]: https://www.crossingtheruby.com/2021/06/22/basic-form-variable-categories.html
