---
layout: post
title: "Rails FormBuilder Innards"
---

The Rails form builder is defined as part of the `ActionView::Helpers` module alongside the `FormHelper` ([source code][rfb]). This is the class that we have previously inherited from, and from which all Rails (HTML) form libraries inherit from (e.g. Bootstrap Form, Formtastic, and Simple Form).

Although I took a brief look at the [Rails FormBuilder][rfb2] in a previous post, the journey into Bootstrap Form piqued my curiosity. Namely the feeling that there is a serious case of over-engineering at play.

After reading through the code I now understand the why both the FormHelper and the FormBuilder are co-located. In the same way that a Bootstrap Form helper method (e.g. `email_field` is overwriting a Rails FormBuilder helper method (`email_field`), the Rails FormBuilder helper method is simply a wrapper for the original Rails FormHelper method (`email_field`). That is the FormBuilder is taking the form/model object in, and using it to automatically populate the relevant configuration of the different HTML attributes (`id`, `value` etc.). But ultimately it is built on the basic form helper. 

So the Rails form helper `email_field` looks like this:

```
def email_field(object_name, method, options = {})
  Tags::EmailField.new(object_name, method, self, options).render
end
```

And the Rails form builder `email_field` is actually dynamically generated as a result of some meta-programming. First the helpers are enumerated in a class attribute `field_helpers` and then the form builder helpers are dynamically generated:

```
class FormBuilder
  …
  # The methods which wrap a form helper call.
  class_attribute :field_helpers, default: [
    :fields_for, :fields, :label, :text_field, :password_field,
    :hidden_field, :file_field, :text_area, :check_box,
    :radio_button, :color_field, :search_field,
    :telephone_field, :phone_field, :date_field,
    :time_field, :datetime_field, :datetime_local_field,
    :month_field, :week_field, :url_field, :email_field,
    :number_field, :range_field
  ]
  …
  (field_helpers - [:label, :check_box, :radio_button, :fields_for, :fields, :hidden_field, :file_field]).each do |selector|
    class_eval <<-RUBY_EVAL, __FILE__, __LINE__ + 1
      def #{selector}(method, options = {})  # def text_field(method, options = {})
        @template.public_send(               #   @template.public_send(
          #{selector.inspect},               #     :text_field,
          @object_name,                      #     @object_name,
          method,                            #     method,
          objectify_options(options))        #     objectify_options(options))
      end                                    # end
    RUBY_EVAL
  end
end
```

So for `email_field` the resulting method turns out to be a simple wrapper around the original helper:

```
def email_field(method, options = {})
  @template.email_field,
    @object_name,
    method,
    objectify_options(options))
end 
```

The `@object_name` has also been dynamically calculated on the basis of the form/model object that was passed into the builder. The method is passed on without modification and the options are merged with the defaults and the object is also added.

If we take a closer look at the `@object_name` this in a few different ways depending on what is passed in to `form_for`. If the first argument is a String or a Symbol (e.g. "email_subscription", :email_subscription) or if there is an explicit name passed with the `:as` option then it will use that. If there is a record or an array of records it will base the name on the object itself. It relies on `ActiveModel::Naming`/`Name`. It takes the class name and then calculates a wide range of attributes that can be used as the basis for producing a name in different contexts.

```
module ActiveModel
  class Name
    …
    def initialize(klass, namespace = nil, name = nil, locale = :en)
      @name = name || klass.name
    
      raise ArgumentError, "Class name cannot be blank. You need to supply a name argument when anonymous class given" if @name.blank?
    
      @unnamespaced = @name.delete_prefix("#{namespace.name}::") if namespace
      @klass        = klass
      @singular     = _singularize(@name)
      @plural       = ActiveSupport::Inflector.pluralize(@singular, locale)
      @uncountable  = @plural == @singular
      @element      = ActiveSupport::Inflector.underscore(ActiveSupport::Inflector.demodulize(@name))
      @human        = ActiveSupport::Inflector.humanize(@element)
      @collection   = ActiveSupport::Inflector.tableize(@name)
      @param_key    = (namespace ? _singularize(@unnamespaced) : @singular)
      @i18n_key     = @name.underscore.to_sym
    
      @route_key          = (namespace ? ActiveSupport::Inflector.pluralize(@param_key, locale) : @plural.dup)
      @singular_route_key = ActiveSupport::Inflector.singularize(@route_key, locale)
      @route_key << "_index" if @uncountable
    end
  …
end
```

You can see that it leans heavily on the `ActiveSupport::Inflector`. Of all of Rails conveniences this is very much the “magic” that makes an impression. In our quest for simplicity, however, it looks very much like an over-indulgence. Why spend modules and methods and lines of code to automatically discern something that eventually ends up as a string when we can just provide the string up front? A (costly) flight of whimsy.

If we zoom out a little then the form helper is concerned with providing: the right prefix for the `name` attributes, for the `id`; the right `value` from the form object, the right `name` and suffix for the `id`. And dealing with HTML options which will more often than not affect the attributes of the HTML tag, as well as options particular to the method itself.

If you put it like that then you wonder why so much code is required to do something relatively straightforward. And if we take out the need for the naming complexity, does such a form helper inheritance hierarchy make the most sense? What if we just abstracted the methods that generate the connecting elements (`id`, `name`, `for`) and allowed you to use them in the construction of your own helpers?

Interesting questions. Applying Chesterton’s fence to existing code, then it’s always good to understand what a piece of code is doing and why before you start removing it. With Rails you often end up burrowing down the rabbit-hole and never escape the Labyrinth. The Minotaur of complexity and indirection is very real and awaits your every turn.

A library such as Bootstrap Form offers a path through the maze. What if the maze wasn’t there? Amazing.

—*Sunday 20th June 2021.*

[rfb]: https://github.com/rails/rails/blob/869532ccea967f62fc917bd8b6832fdf02d3ed10/actionview/lib/action_view/helpers/form_helper.rb#L1634-L2653
[rfb2]: https://www.crossingtheruby.com/2021/05/10/form-formation-rails-form-builder.html
