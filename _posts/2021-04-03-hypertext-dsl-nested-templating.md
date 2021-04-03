---
layout: post
title: "Hypertext DSL: Nested Templating"
---

In this post I’d like to build on the [inheritance-based extension of Hypertext DSL][ihd] for variables and helpers and see how we might accomplish nested templating.

Nested templating is a common use-case that I looked at [with Mote][swm], and in the most basic case, consists of surrounding a template with a ‘parent’ layout and putting partials into the template as ‘children’. Ideally children can be infinitely nested, that is, partials are able to call partials.

To keep things simple we can keep the context the same, and avoid switching the template context per fragment unnecessarily. Perhaps in future we can pass in variables that are local to the partial and keep it more scoped.

As a reminder, this is the technique we used for processing layouts in Sew with Mote:

```
def render_page
  Mote.parse(page.body, self, page.keys)[page]
end

def render
  @page[:content] = render_page
  Mote.parse(File.read("_layout.mote", self, page.keys)[page]
end
```

That is, a double pass, first rendering the template (page) content and then rendering the layout with the rendered template passed in assigned to the page’s `content` variable.

I can see how we might accomplish it in two different ways. The first is by building up a string of Ruby, including layout, template and partial(s) and then evaluating it once with the Hypertext DSL. The second is to evaluate each individual fragment and piece it all together using `Hypertext::DSL#append`.

Let’s start with the second approach alongside our inheritance based Hypertext DSL extension:

```
require "hypertext"
require "hypertext/dsl"

class Context < Hypertext::DSL
  def initialize(params, &block)
    params.each do |key, val|
      instance_variable_set(sprintf("@%s", key), val)
    end

    super(&block)
  end
end

class Template < Context
  def id_generator
    "id-#{rand(100)}"
  end
end
```

The nested approach would essentially require two instances of the `Template` class with identical parameters, save in the layout’s case, which would carry an additional `content` parameter with a value of the rendered template.

A rough sketch of the code looks like this:

```
params = { page_title: "Hello, World!" }

template = Template.new page_title: "Hello, World!" do
  h1 id: id_generator do
    text: "Welcome"
  end
end

layout = Template.new params.merge(content: template.to_s) do
  html lang: "en-GB" do
    head do
      title do
        text @page_title
      end
    end
    body do
      append @content
    end
  end
end

puts layout.to_s
```

This gets us all of the way there:

```
# =>
# <html lang="en-GB">
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <h1 id="id-23">
#   Welcome
# </h1>
# 
#   </body>
# </html>
```

We’ll leave that on the shelf for now and take a moment for partials. In principle both layouts and partials should work in the same way; the layout would correspond to the template and the template would correspond to the partial. However, with the layout above, we operated outside of the context of an individual template and for partials we’d much rather just be able to call a helper method from the template and render it in place.

We already have the `append` method which we can use for including partials. A purist might alias it as `partial` but we can stick with `append` for now. `append` takes a string, so the output of whatever we pass to it must be a string.

If we take the heading and extract it into a partial under a helper named `heading` then let’s see what we can cobble together.

Now when putting the following code together, it occurred to me that we are already in the `Hypertext::DSL` context, so instead of appending a string we could just use our DSL’s hypertext definition and call our helper method in the template directly. No need to mess about with intermediate string states. This is what that looks like:

```
class Template < Context
  …
  
  def heading
    h1 id: id_generator do
      text: "Welcome"
    end
  end
end
```

And then calling the partial in the template itself with an additional paragraph for good measure:

```
template = Template.new page_title: "Hello, World!" do
  heading
  p do
    text "A Hypertext template."
  end
end
```

Running the template (this time without the layout) now gives us clear evidence that our partial `heading` method works and can be called directly.

```
puts template.to_s

# =>
# <h1 id="id-91">
#   Welcome
# </h1>
# <p>
#   A Hypertext template.
# </p>
```

That’s really nice. Maybe we can define our layout like this too. Let’s override the `to_s` method, assuming that every template should be rendered with the layout.

```
  def to_s
    content = super                                      
    @ht = Hypertext.new                                  
  
    instance_eval do
      html lang: "en-GB" do
        head do                                          
          title do                                       
            text @page_title                             
          end
        end
        body do                                          
          append content                                 
        end
      end
    end                                                  
                                                         
    @ht.to_s                                             
  end
end
```

We’re playing some tricks here, and I have to say up front that I’m not sure I like how I got this working, but let’s consider it before judging.

We’re grabbing the rendered content of the template by calling `super` which calls the `to_s` defined in `Hypertext::DSL` and storing it in a local variable `content`.

Then we’re resetting the `@ht` instance variable, clearing out all of the template’s AST, so that the variable is an empty array. Then we evaluate the layout’s definition using `instance_eval` which will build up an AST for the layout, and as part of that layout definition we are appending the template’s content from the local variable `content` and then calling `to_s` directly on the `Hypertext` instance (to avoid an infinite loop) which renders both template and layout together.

```
puts template.to_s

# =>
# <html lang="en-GB">
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <h1 id="id-76">
#   Welcome
# </h1>
# <p>
#   A Hypertext template.
# </p>
# 
#   </body>
# </html>
```

That’s a successful experiment. I’m going to reserve judgement until I investigate how I might load layouts, templates and partials from files. Adding this element may necessitate a change in approach which means any strong opinions at this point might be rendered moot.

We’ve explored two different ways of rendering a layout: one with machinery ‘outside’ the HTML definition instantiating two different classes and nesting one inside the other; and the other with machinery ‘inside’ the HTML definition using only one instance of the class (but cheating a little by resetting the accumulating instance variable). Maybe there are other ways I’ve missed?

—*Saturday 3rd April 2021.*

[ihd]: https://www.crossingtheruby.com/2021/03/31/hypertext-dsl-api-extension-via-inheritance.html
[swm]: https://www.crossingtheruby.com/2021/02/22/rendering-with-mote-in-sew.html
