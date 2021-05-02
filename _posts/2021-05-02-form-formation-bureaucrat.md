---
layout: post
title: "Form Formation: Bureaucrat"
---

In previous posts I looked at Ruby form libraries available both [with][rails] and [outwith][alt] Rails. One of the form libraries that I overlooked was [Bureaucrat][bur]. It was nestled in the acknowledgements section of [Scrivener’s README][ack].

It is quite the oversight because Bureaucrat offers a very interesting counterpoint to the libraries that have been listed in previous posts.

As mentioned, most libraries operate either as a form object or for HTML generation, but not both. And most libraries that do HTML generation choose an imperative approach.

Bureaucrat handles both the form object and the HTML generation. In doing so, it constructs the field HTML declaratively. 

A _form_ object has many _fields_ and each field has a _widget_. It allows for the definition and applying of _validations_ which result in _errors_ or _cleaned data_ (a representation of the valid field data).

```
Form
├─ Field -> Widget
├─ Field -> Widget
└─ Field -> Widget
```

Each field has a `to_s` method which renders the field to an HTML string. This is delegating to the field’s corresponding widget’s `render` method. The widgets defined in Bureaucrat correspond to the different HTML form controls, e.g., a text input, a password input, a text area, etc.

I think you need to set up the `<form>` tag yourself and then iterate through the fields one-by-one, so it’s not completely declarative, this being an imperative step, but in the main it’s radically different from other tools in terms of HTML generation.

I spotted this example in Bureaucrat’s tests:

```
<form method="POST">
  <% form.each do |field| %>
    <% with_errors = !field.errors.empty? %>
    <div class="<%= field.css_classes('field') %>">
      <%= field.label_tag %>
      <%= field %>
      <% unless field.errors.empty? %>
        <span class="error">
          [x]
          <span class="field-errors">
            <%= field.errors %>
          </span>
        </span>
      <% end %>
      <% unless field.help_text.empty? %>
        <span class="help">
          [?]
          <span class="help-text">
            <%= field.help_text %>
          </span>
        </span>
      <% end %>
    </div>
  <% end %>
  <button>Submit</button>
</form>
```

(It looks similar in structure to the [form helper][fh] I put together in Hypertext)

As you can see, the fields don’t just handle the HTML generation of the form control, but also the `label` tag and the error messages. There is a way to customise the CSS classes that will be applied, so it is intended to have some flexiblity. I assume is also possible to define your own widgets and connect them to fields yourself should more customization be necessary.

I like these general principles that underpin Bureaucrat. It handles the form as an object that has access to everything necessary for the form’s lifecycle. It has clear boundaries by drawing the circle around a form and all of its constituent parts, differing from other tools which ask you to imperatively mix two ‘circles’ together (form object + HTML generation). 

—*Sunday 2nd May 2021.*

[rails]: https://www.crossingtheruby.com/2021/04/30/form-formation-rails-libraries.html
[alt]: https://www.crossingtheruby.com/2021/05/01/form-formation-ruby-libraries.html
[bur]: https://github.com/tizoc/bureaucrat
[ack]: https://github.com/soveran/scrivener#see-also
[fh]: https://www.crossingtheruby.com/2021/04/14/fine-tuning-a-form-helper-in-hypertext.html
