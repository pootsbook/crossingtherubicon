---
layout: post
title: "Basic Form: Field Presentation"
---

After [decorating our `Field`][dof] class we now have everything we need to render our field to HTML.

We can start for simplicity’s sake with one method, and if necessary split it out into smaller pieces.

We have any number of method names to choose: `to_s`, `to_html`, and `render`. I’m going to go with the first as a Ruby convention.

In terms of building up our HTML I’ll use simple string interpolation for now.

```
class Field
  …
  def to_s
    %Q(
      <div class="form-group">
        <label for="#{input_id}">
          #{label}
        </label>
        <input 
            type="#{type}" 
            name="#{input_name}"
            value="#{value}"
            class="form-control" 
            id="#{input_id}" 
            aria-describedby="#{help_id}">
        <small 
            id="#{help_id}" 
            class="form-text text-muted">
          #{help}
        </small>
      </div>
    )
  end
end
```

The only thing that hasn’t been included is the error HTML. That’s a little trickier when doing it this route.

Let’s add a couple of methods to round out this example with the knowledge in the back of our head that a more gracious templating approach would invalidate the need for these methods.

```
class Field
  …
  def error_class
    errors.any? ? "is-invalid" : ""
  end
  
  def error_text
    errors.join(", ")
  end
  
  def error_html
    %Q(
      <div class="invalid-feedback">
        #{error_text}
      </div>
    )
  end
end
```

These additions allow us to modify the `to_s` method as follows:

```diff
  class Field
    …
    def to_s
      %Q(
        <div class="form-group">
          <label for="#{input_id}">
            #{label}
          </label>
          <input 
              type="#{type}" 
              name="#{input_name}"
              value="#{value}"
-             class="form-control"
+             class="form-control #{error_class}"
              id="#{input_id}" 
              aria-describedby="#{help_id}">
+         #{error_html}
          <small 
              id="#{help_id}" 
              class="form-text text-muted">
            #{help}
          </small>
        </div>
      )
    end
  end
```

And that completes the field experiment in our basic form. 

The process of pulling everything apart and putting it back together again has been helpful in concretely visualising the more abstract distinctions we had made in the concepts with code.

It also makes very clear just how many subtle layers there are in arriving at the final HTML, and also how much the form/model object and the choice of templating approach influence the final solution.

—*Sunday 4th July 2021.*

[dof]: https://www.crossingtheruby.com/2021/07/03/basic-form-field-decoration.html
