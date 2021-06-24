---
layout: post
title: "Basic Form: Formtastic"
---

If we turn our attention to Formtastic then we can create a form using our model object from [default Rails][dr].

Formtastic is a layer of syntactic sugar upon Rails default form builder.

```
<%= semantic_form_for @form,
    as: :subscription,
    url: email_subscriptions_path do |f| %>
  <%= f.input :email, 
      label: "Email address",
      hint: "We'll never share your email with anyone else." %>
  <%= f.action :submit, 
      as: :button,
      label: "Submit" %>
<% end %>
```

I think this is enough to get us what we want. There is one distinction worth noting: Formtastic defaults to an `li` as a wrapper for the form-control. That’s not such a big deal, and more semantically correct if we’re being pedantic.

I think there are a few things that are interesting here. Given our [categories][bfc] and the [hierarchy][bfh] then Formtastic really does seem to capture the definition of a field at the right level. 

The attribute, label and hint are all specified at the top level, being the key concerns in the template. Formtastic automatically chooses `input[type=email]` based on the attribute name containing the string `"email"`, so the mapping is implicit.

The errors are invisible being an internal concern. The input is the central element that is then _wrapped_ by the hint/help text and, optionally, the error message.

Here we see that explicitly in the code:

```
# Formtastic::Inputs::Base::Wrapping

def input_wrapping(&block)
  template.content_tag(:li,
    [template.capture(&block), error_html, hint_html].join("\n").html_safe,
    wrapper_html_options
  )
end
```

Where the input is itself wrapping the original Rails method:

```
# Formtastic::Inputs::EmailInput

def to_html
  input_wrapping do
    label_html <<
    builder.email_field(method, input_html_options)
  end
end
```

The label lives together with the field.

If we flatten that out then we see all four aspects:

```
label_html
email_field
error_html
hint_html
```

I think I like this succinctness but part of me would prefer this mapping to live outside of the template itself. 

—*Thursday 24th June 2021.*

[dr]: https://www.crossingtheruby.com/2021/06/17/basic-form-rails.html
[bfc]: https://www.crossingtheruby.com/2021/06/22/basic-form-variable-categories.html
[bfh]: https://www.crossingtheruby.com/2021/06/23/basic-form-variable-hierarchy.html
