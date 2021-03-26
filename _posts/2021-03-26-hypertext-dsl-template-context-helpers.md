---
layout: post
title: "Hypertext DSL: Template Context (Helpers)"
---

When looking at ways in which to [expose variables][ev] to a Hypertext HTML template in a previous post, I had to do a bit of meta-programming and evaluation to arrive at a solution. I’m not sure if its an optimal solution but it is a working solution for now.

In this post I would like to explore how we might expose helper methods to the Hypertext DSL context so that we could in principle write methods at a level above the HTML definition to enable further abstractions.

I had imagined two different routes. The first was the creation of a `Context` class, containing helper methods, which was the context for executing and rendering templates defined in the Hypertext DSL. This is similar to the implementation of my static site generator Sew, using Mote. When [experimenting][ev] with that approach it seems that the nested use of `instance_eval` prevented access to the outer `Context` methods from the inner Hypertext DSL execution.

The second route I had in my mind is essentially the same approach but then flipped around. Rather than execute Hypertext DSL in this parent context, can we hand a context down to Hypertext DSL which it can then use.

`Context` would then become a module and we would include that module in the same scope as Hypertext DSL.

```
module Context
  def automatic_id
    "el-#{rand(1000)}"
  end
end
```

What was our `Context` _class_ can now become a renderer:

```
require "hypertext"
require "hypertext/dsl"

class Renderer
  def render(template, context_module)
    instance_eval <<-CODE
      include #{context_module}
      Hypertext::DSL.new do
        #{File.read(template)}
      end.to_s
    CODE
  end
end
```

And then our template `index.ht` using our helper method:

```
html lang: "en-GB" do
  head do
    title do
      text "Hello, World!"
    end
  end
  body do
    h1 id: id_generator do
      text "Welcome"
    end
  end
end
```

And then a script to pull it all together:

```
require_relative "context"
require_relative "renderer"

puts Renderer.new.render("index.ht", Context)
```

This breaks down. We can’t call `include` there and import the module at that level. And I believe the nested `instance_eval` methods mean that methods would not be accessible to that scope.

A third route that comes to mind, is to patch Hypertext DSL to take a context and use that context as the execution context for Hypertext. We switch `Context` back to a class:

```
class Context
  def automatic_id
    "el-#{rand(1000)}"
  end 
end
```

We monkey patch Hypertext DSL:

```
require "hypertext"
require "hypertext/dsl"

class Hypertext
  class DSL
    def initialize(context, &block)
      context.instance_eval(&block)
    end
  end
end
```

and update the renderer to take a context object, passing it into the initialization of the Hypertext DSL:

```
class Renderer
  def render(template, context_object)
    instance_eval <<-CODE
      Hypertext::DSL.new(#{context_object}) do
        #{File.read(template)}
      end.to_s
    CODE
  end
end
```

We can then update our script:

```
require_relative "./context"
require_relative "./renderer"

puts Renderer.new.render("index.ht", Context.new)
```

And our new context works, but it overrides the original context which provides all the HTML methods. I hazard a guess that the `@ht` instance variable would also not be available.

That exhausts this line of enquiry for now, and makes it clear that some of these things might be better baked into Hypertext itself after being through through a little more or I have to be a little more creative in coming up with a solution.

—*Friday 26th March 2021.*

[ev]: https://www.crossingtheruby.com/2021/03/24/hypertext-dsl-template-context-variables.html
