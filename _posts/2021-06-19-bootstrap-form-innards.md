---
layout: post
title: "Bootstrap Form Innards"
---

Bootstrap Form has components for each of the three elements that we’d recognised as being different from Rails defaults: 

- hints / help text (`BootstrapForm::Components::Hints`)
- labels (`BootstrapForm::Components::Labels`)
- in-line errors (`BootstrapForm::Components::Validation`)

A fourth aspect is the layout of the form, building on top of the different ways in which a Bootstrap form can be rendered: vertical (default); horizontal; in-line (`BootstrapForm::Components::Layout`).

The hint is then generated with the following method:

```
def generate_help(name, help_text)
  return if help_text == false || inline_error?(name)


  help_klass ||= "form-text text-muted"
  help_text ||= get_help_text_by_i18n_key(name)
  help_tag ||= :small


  content_tag(help_tag, help_text, class: help_klass) if help_text.present?
end
```

It returns nothing if there is no help text (the in-line condition on the last line of the method `if help_text.present?`). It also returns nothing if the `help_text` parameter is explicitly set to false. And the third instance where it returns nothing is related the display of in-line errors. If this configuration is set then it looks like an error will replace the help text.

The rest of the method follows the Rails View Helpers pattern. They often involve taking in the options and merging those with library or configured defaults, set up any items unique to this particular method and its HTML output, then call the `content_tag` method to generate the HTML.

The in-line errors are almost identical:

```
def generate_error(name)
  return unless inline_error?(name)


  help_text = get_error_messages(name)
  help_klass = "invalid-feedback"
  help_tag = :div


  content_tag(help_tag, help_text, class: help_klass)
end
```

It ensures there are indeed errors present for that attribute before continuing. Then it fetches the error messages from the form/model object (available as the main object passed in when instantiating a form builder). Then it does some custom setup (the HTML class attribute) before rendering the HTML via Rails’ `content_tag`.

The `generate_label` method does something similar for labels, but ultimately calls out to the Rails `label` method.

```
def generate_label(id, name, options, custom_label_col, group_layout)
  return if options.blank?

  options[:for] = id if acts_like_form_tag

  options[:class] = label_classes(name, options, custom_label_col, group_layout)
  options.delete(:class) if options[:class].none?

  label(name, label_text(name, options), options.except(:text))
end
```

The actual `email_field` method requires a little more sleuthing to uncover. 

First there is a definition per input that all resemble one another. This is `email_field`:

```
module BootstrapForm
  module Inputs
    module EmailField
      extend ActiveSupport::Concern
      include Base

      included do
        bootstrap_field :email_field
      end
    end
  end
end
```

It’s relying on the `bootstrap_field` method in the `BootstrapForm::Inputs::Base` module.

```
class_methods do
  def bootstrap_field(field_name)
    define_method "#{field_name}_with_bootstrap" do |name, options={}|
      form_group_builder(name, options) do
        prepend_and_append_input(name, options) do
          send("#{field_name}_without_bootstrap".to_sym, name, options)
        end
      end
    end

    bootstrap_alias field_name
  end
  
  …
end
```

The `class_methods` method is a bit of Rails syntactic sugar in `ActiveSupport::Concern` to define class methods. It defines (in our case) `email_field_with_bootstrap` which calls out to methods in Bootstrap Form to wrap the original Rails input method. Then it does some jiggery-pokery by swapping the Bootstrap Form method for the Rails method and vice versa. This means that calling `email_field` on a form builder will call the Bootstrap Form method rather than the Rails method. But Bootstrap Form ultimately wants to use the Rails method for the final output and needs a reference to it.

It’s all very circuitous. And a little bit impenetrable if you don’t have the patience or wherewithal to dive into the code. 

As an aside, from my perspective, this is unnecessary complexity and indirection. If you stop to reconsider that what we are trying to do is build a simple string of HTML, then we really have missed the forest for the trees.

—*Saturday 19th June 2021.*
