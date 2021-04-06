---
layout: post
title: "Hypertext DSL: Reading (Nested) Templates from File (2)"
---

In the first post on [nested templating][nt] I successfully rendered a layout and template by creating two instances of a `Template` class which inherited from `Hypertext::DSL` and combining the output together with `Hypertext::DSL#append`.

The other solution in the same post was to modify the `initialize` method with a hard-coded layout.

Neither option feels right to me at this stage, and although I mimicked the double-instance approach when [loading from file][lff] I feel that we might better be able to build on the approach used to load partials.

> In a future post I’d like to explore if we can have “turtles all the way down” and integrate the layout, template and partial using the same technique.

The implementation of a `partial` method in our `Template` class is a testament to our desired simplicity:

```
def partial(filename)
  instance_eval File.read(filename)
end
```

When `partial` is called from a fragment with the name of a file containing another Hypertext DSL fragment then it simply reads the file and evaluates it in the current context. An additional benefit here is that it also executes it in the correct ‘position’ in the document’s AST so that it appears in the correct output location. There is no need to piece together previously executed fragments after rendering using `append`. It feels right.

This is how it would be called:

```
partial "header.ht"
p do
  text "A Hypertext template."
end
```

To then combine the layout and the template in the same fashion that we do a template and a partial, without instantiating two `Template` objects, then we’ll need to pass in references to both the layout and the template file when creating the template.

There are a couple of design decisions to consider. For starters, we already have a `params` hash. Now strictly speaking the `params` hash is for populating a set of instance variables for interpolation in the template output. However, this could also be used as a transport mechanism for template and layout information.

The alternative would be to have dedicated arguments for either template or layout, or both.

Concretely, our `Context#initialize` function could have one of the following signatures:

```
# Using params for everything

params = { layout: "layout.ht", template: "template.ht" }
def initialize(params)

# Using args for everything

def initialize(params, template, layout)

# Using a combination, either

params = { layout: "layout.ht" }
def initialize(params, template)

# or

params = { template: "template.ht" }
def initialize(params, layout)
```

Let’s go with passing everything through in the params for now. One side of the trade-off is that these keys in the hash (`template` and `layout`) become ‘reserved’ and that they are less explicit and could therefore be considered ‘magical’. The other side is that it is a simple API with no need to remember various arguments and their order. (And strictly speaking, `@template` is a variable just like the others.) We can always pass judgement later.

If we begin with our `Context` class, and modify the `initialize` method:

```diff
  class Context < Hypertext::DSL
-   def initialize(params, fragment)
+   def initialize(params)
      params.each do |key, val|
        instance_variable_set(sprintf("@%s", key), val)
      end
  
      @ht = Hypertext.new
-     instance_eval fragment
+     instance_eval File.read(@layout)
    end
  end
```

The `layout` key from the `params` hash will be assigned to the `@layout` instance variable. We can then read the contents of the file with that name and evaluate it in the context of the current instance.

Now for this to work together with the template, then we’ll need to include a means of executing the template as a partial.

```diff
  # layout.ht

  html lang: "en-GB" do
    head do
      title do
        text @page_title
      end
    end
    body do
-     append @content
+     partial @template
    end
  end
```

Given we assume the presence of the `partial` method in this scenario let’s move it up from our `Template` class into our `Context` class:

```diff
  class Context < Hypertext::DSL
    def initialize(params)
      …
      @ht = Hypertext.new
      instance_eval File.read(@layout)
    end
  
+   def partial(filename)
+     instance_eval File.read(filename)
+   end
  end
```

The eagle-eyed among you will have spotted that there is now no difference between the evaluation we are doing in the `initialize` method and the `partial` method, so we can further simplify by getting rid of the duplication:

```diff
  class Context < Hypertext::DSL
    def initialize(params)
      …
      @ht = Hypertext.new
-     instance_eval File.read(@layout)
+     partial @layout
    end
  
    def partial(filename)
      instance_eval File.read(filename)
    end
  end
```

And there we have it. Turtles all the way down. The Russian dolls can dance. We have implemented all of our rendering execution in terms of one simple primitive.

If we then test this out on a layout, template and partial:

```
# layout.ht

html lang: "en-GB" do
  head do                                          
    title do                                       
      text @page_title                             
    end
  end
  body do                                          
    partial @template
  end
end

# template.ht

partial "header.ht"
p do
  text "A Hypertext template."
end

# header.ht (partial)

h1 id: id_generator do
  text "Welcome"
end
```

And create our `Template` object, rendering it to an HTML string:

```
require "hypertext"
require "hypertext/dsl"

class Context < Hypertext::DSL
  def initialize(params)
    params.each do |key, val|
      instance_variable_set(sprintf("@%s", key), val)
    end

    @ht = Hypertext.new
    partial @layout
  end

  def partial(filename)
    instance_eval File.read(filename)
  end
end

class Template < Context
  def id_generator
    "id-#{rand(100)}"
  end
end

params = {
  layout: "layout.ht",
  template: "template.ht",
  page_title: "Hello, World!"
}
template = Template.new(params)
puts template.to_s
```

This does exactly what we expect it to do:

```
# =>
# <html lang="en-GB">
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <h1 id="id-77">
#       Welcome
#     </h1>
#     <p>
#       A Hypertext template.
#     </p>
#   </body>
# </html>
```

I’m really happy with this. The `Context` class is the meat and potatoes of this implementation, building a small templating layer on top of the Hypertext DSL. 

It’s 12 SLOC (14 if you include the `require`s). 

It introduces one method, `partial` which enables the infinite nesting of Hypertext DSL fragments and allows a straightforward way of handling layouts, templates and partials.

The layout, template and partials are all executed in the same context, meaning all helpers and variables are available at each level of the hierarchy.

It is a foundation for one’s own `Template` or `Renderer` class which can then be expanded with helper methods and other, extra desired functionality.

The question about the appropriate method signature fades away in the light of the appropriateness of the rest of the solution to this problem. It is a side-show.

What do you think? Are you as delighted with this seemingly final conclusion as I am? Do you see other ways to do this? More elegant approaches? Have I missed anything?

—*Tuesday 6th April 2021.*

[nt]: https://www.crossingtheruby.com/2021/04/03/hypertext-dsl-nested-templating.html
[lff]: https://www.crossingtheruby.com/2021/04/05/hypertext-dsl-reading-nested-templates-from-file.html
