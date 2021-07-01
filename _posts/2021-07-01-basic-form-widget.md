---
layout: post
title: "Basic Form: Widget"
---

I’m going to front up and admit that I got lost among the Bureaucrat widgets. The key challenge is that a widget does not have the field object exposed to it, but only a small subset of the field attributes. A field has a widget, but a widget does not have access to a field.

To be as comprehensive as I would like to be, and to broaden the responsibility of a widget to the whole field HTML, including a label, errors, and a hint in addition to the form control then a widget would need access to the field object itself.

My imagined API goes something like this:

```
field = Field.new
widget = Widget.new(field)
widget.render
```

To give control back to the field itself and make it responsible for rendering itself then you could do the following:

```
class Field
  def to_s
    Widget.new(field).render
  end
end
```

This is obviously a little simplistic—there are no avenues for configuration, nor means for deciding which widget should be used—but let’s run with this for now.

A widget is then responsible for querying the field to get the necessary information:

```
class Widget
  attr_accessor :field
  
  def initialize(field)
    @field = field
  end
  
  def render
    # HTML
  end
end
```

The class could be filled out with methods that query the field object to generate the appropriate matching `id`/`for` attribute pairs, the `name` and so on. These methods could then be called from the `render` method to put everything in place in the HTML.

I think that’s how I would design it. Whether you then need a widget to separate this from the field is another matter (versus keeping it all in the `Field` class) but the principle is the same.

—*Thursday 1st July 2021.*
