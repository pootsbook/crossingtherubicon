---
layout: post
title: "Form Formation: Rails ViewComponents"
---

As part of my research into how the Ruby community does forms I was talking to a friend with whom I shared the same workspace a number of years ago. Like me, he has settled into a technical lead role at a startup after a period of contracting.

The startup has pivoted a number of times and as a result the current iteration, which is seeing a lot of traction, was essentially written from scratch at the beginning of this year. The amount they have accomplished is astonishing and a testament to the ‘rebirth’ of modern Rails.<sup>1</sup> They are using all the new and shiny toys like Hotwire with Turbo and Stimulus.

This application is all about data collection and is thus filled left, right and centre with forms. To accomplish so much in such a short space of time requires some foresight and planning. It was therefore interesting to see that they had written their own form builder, a descendent of the Rails form builder, to declare the mapping between attribute and form-control in the HTML. What they had also done is more interesting. In this form builder they had essentially rewritten the form helpers to be completely customised to their particular use cases. And not only that, but many of the form helpers themselves used GitHub’s [ViewComponents][vc] internally.

Why is this interesting? Well, a ViewComponent looks a lot like a “widget” from Django or Bureaucrat. It’s a lot more than that but the principles are the same.

I should write about ViewComponents in detail another time, my initial, visceral reaction was that this was another step in the React-ification/JSX-ification of the back-end, something you see in Phoenix LiveView as well with [Surface][sf] and now [HEEX][heex]. I’m not the biggest fan.

But ViewComponents do seem like a good step in the right direction, providing the encapsulation of, well, view components, in a way that is more Ruby-ish than bare ERB partials.

It’s a shifting of the buckets, putting a bucket that’s designed to contain the contents of both presenter and partial buckets in one.

Now the motivation for ViewComponents seem very much aligned with my stated goals for form formation:

> ViewComponents are most effective in cases where view code is reused or benefits from being tested directly

So they seem a perfect fit for form fields and form controls. Let’s give it a go with [yesterday’s `textarea`][yt].

We first have to subclass `ActionView::Helpers::FormBuilder`:

```
class ComponentFormBuilder < ActionView::Helpers::FormBuilder
  ...
end
```

Then we override the `text_area` method:

```
def text_area(object_name, method, options = {})
  super(object_name, method, options)
end
```

Of course this will do nothing different as its calling out to the same method on the superclass, but it is a good starter. If we wanted to automatically add a field wrapper and the label as well as the text area then we could accomplish that (without a ViewComponent) as follows, remembering that the builder exposes the template via `@template`.

```
def field_wrapper(method)
  @template.content_tag(:div, { class: "field", id: "field-#{method}" }) do
    label(@object, method) +
    block.call
  end
end

def text_area(method, options = {})
  field_wrapper(method) do
    super(method, options)
  end
end
```

A little contrived perhaps, but it illustrates the principle.

If we were to go further and hook in the Stimulus controller we could do so as follows, having neatly created a layer of abstraction between the template and the default form builder:

```
def text_area(method, options = {})
  options.merge!({
    'data-target': 'text-saver.input',
    'data-text-saver-key': "#{@object_name}-{@object.id}",
    'data-action': 'keyup->text-saver#changed'
  })

  field_wrapper(method) do
    super(method, options)
  end
end
```

Now, if we specify our custom form builder when declaring a form we’ll get all of the above out of the box:

```
<%= form_for @ticket, builder: ComponentFormBuilder do |f| %>
  <%= f.text_area(:question) %>
  …
<% end %>
```

If we wanted to encapsulate the text area in a ViewComponent then we could come at it from another angle. Instead of calling to the superclass we could reimplement our text area as a component.

```
def text_area(method, options = {})
  field_wrapper(method) do
    @template.render(TextAreaComponent.new(object: @object, field: method))
  end
end
```

The component would start something like this (which I understand is a very simplistic reimplementation of what Rails gives you out of the box with its form helpers):

```
class TextAreaComponent < ViewComponent::Base
  attr_accessor :object, :field
  
  def initialize(object:, field:)
    @object = object
    @field = field
  end
  
  def value
    @object.public_send(field)
  end
  
  def id
    "#{object_name}_#{field}"
  end
  
  def name
    "#{object_name}[#{field}]
  end
  
  def key
    "#{object_name}-#{object.id}"
  end
  
  def options
    {
      'data-target': 'text-saver.input',
      'data-text-saver-key': key,
      'data-action': 'keyup->text-saver#changed'
    }
  end
  
  private
  
  def object_name
    @object_name ||= object.class.to_s.underscore
  end
end
```

And the component template like this:

```
<%= content_tag :text_area, value, options %>
```

There are indeed many ways to skin a cat.

I’m not sure this is the best possible example to act as an advertisement for ViewComponents, but we see how the component class keeps the HTML tidy by abstracting most of the grunt work into the component. (I can’t help getting the feeling that this is very much a step closer in intent towards Hypertext but on a very different path—Hypertext wants Ruby and templates to be indistinguishable, ViewComponents want templates and Ruby, the components, to be _more_ distinguishable).

The overarching thread is clear though, a custom form builder with components is a valid way of automating the construction of forms in your application by giving you a bucket to put the abstracted parts that is neatly divided from the template. The template remains simple and predictable, the helpers and component hold the complexity that can be easily applied across all form instances.

—*Saturday 15th May 2021.*

[vc]: https://viewcomponent.org
[yt]: https://www.crossingtheruby.com/2021/05/14/form-automation-benefit-persisting-unfinished-forms-programmatically.html
[sf]: https://surface-ui.org
[heex]: https://github.com/phoenixframework/phoenix_live_view/pull/1440


<div class="footnotes">
  <ol>
    <li>
      You could see some of the latest additions to the Rails framework as a rebirth of <em>Rails</em>, but you could just as easily say that the rise in interest can be attributed to the fact that we, the <em>developers</em>, have been reborn after a sorry death in the unfulfilled promises of client-side JavaScript.
    </li>
  </ol>
</div>
