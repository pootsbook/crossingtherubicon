---
layout: post
title: "Form Formation: Hanami Validation Integration
---

In yesterday’s post I showed how to define and run [validations in Hanami][hv]. I later learned that Hanami’s approach of mixing in modules rather than using inheritance is a core tenet of the framework.

What I didn’t uncover was where in the greater whole validations are defined, and how the error messages are returned to the HTML form.

What is fascinating about Hanami validations is that they mirror the popular use of schemas and changesets in Phoenix _at the web or controller layer_. Although this is not the ‘blessed’ approach in the framework, with schemas and validations being run far closer to the models and business logic, it has two notable proponents; [Saša Jurić][sjc] and [Aaron Renner][ar] both consider casting, filtering and validation essential at the controller level, next to the form, and Hanami agrees.

Hanami controllers, or rather _actions_ have a DSL to apply type casting, filtering and validation with a built-in DSL:

```
module Web::Controllers::Signup
  class Create
    include Web::Action

    params do
      required(:email).filled
      required(:password).filled

      required(:address).schema do
        required(:country).filled
      end
    end

    def call(params)
      …
    end
  end
end
```

The params hash, which will contain the values submitted via a form can be validated in the controller.

If validation fails then the template is rendered again with the current params hash and validation error messages can be fetched on that object:


```
<% unless params.valid? %>
  <div class="errors">
    <h3>There was a problem with your submission</h3>
    <ul>
      <% params.error_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
    </ul>
  </div>
<% end %>
```

It should be possible to then extend this to an `error` helper for any given attribute so that validation messages can be co-located with the form control.

Interesting to note that Hanami continues to keep the “form object” distinct from these concerns, choosing instead to handle everything on the `params` hash. Or, looking from a different perspective, the params hash _is_ the form object and this is decoupled from the model.

On a slightly unrelated note I quite like this division in the grander scheme of application architecture. It pushes validation concerns to the boundary of the application (receiving form data from the outside world at the controller layer) allowing the internal business logic to work with valid and type-cast data.

—*Friday 11th June 2021.*

[hv]: https://www.crossingtheruby.com/2021/06/10/form-formation-hanami-validations.html
[sjc]: https://medium.com/very-big-things/towards-maintainable-elixir-the-core-and-the-interface-c267f0da43
[ar]: https://aaronrenner.io/2019/09/18/application-layering-a-pattern-for-extensible-elixir-application-design.html
