---
layout: post
title: "Basic Form: Bureaucrat (3)"
---

The widget associated with a Bureaucrat field is limited to the form control itself. The label, help text and errors are not included in a widget out of the box.

Reviewing the field class revealed a `label_tag` method which produces a label tag with the correct value for the `for` attribute, associating it with the `input`’s `id` element.

I must admit I didn’t quite understand why presentation concerns like this were mixed up with the field class while the widget, which I assumed would deal with all of the presentation logic, only produced the form control itself.

In a template the following code would be necessary to display all of the different facets of a field:

```
<div class="form-group">
  <%= field.label_tag %>
  <%= field %>
  <% unless field.errors.empty? %>
    <div class="invalid-feedback">
      <%= field.errors %>
    </div>
  <% end %>
  <% unless field.help_text.empty? %>
    <small class="form-text text-muted">
      <%= field.help_text %>
    </small>
  <% end %>
</div>
```

I feel like this defeats the purpose of abstracting away the form control into a widget. Either the widget should deal with everything, or there should be no widget.

To compare it with Rails, the widget is the raw `input_tag` helper, whereas my sensibilities would want a widget to be more like an all-inclusive Formtastic or Simple Form helper.

So while Bureaucrat starts down a declarative path, for me it doesn’t go far enough. The inclusion of a `label_tag` in the field, but nothing for errors or help text is a little short-sighted. The argument might be that you can easily extend Bureaucrat to add them, and that’s a fair point. But to arrive at a solution that caters to all of the elements of a form control then also needs the introduction of a custom widget. Perhaps we can take advantage of the inheritance hierarchy to make this a reality.

—*Wednesday 30th June 2021.*
