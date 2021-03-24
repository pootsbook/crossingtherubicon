---
layout: post
title: "Hypertext DSL: Template Context (Variables)"
---

When doing some [template explorations][hte] with the Hypertext DSL, I arrived at encapsulating templates in methods, passing parameters into the methods to provide variables for interpolating in the templates.

This is one approach to providing the necessary data to the template. However, data is not the only aspect that we might need. If we were to introduce a higher level API, built on top of the DSL, to construct, for example, a form, then we’ll need a template context in which the DSL will be executed and which exposes extra, supra-DSL methods.

And if we have a context it might also be able to provide the variables for interpolation.

With our [Markaby experiment in Syro][meis], we have already seen how we might go about creating this context and executing the template files using `instance_eval`. Let’s see if we can use the same trick with Hypertext.

Let’s take our prototypical example of the definition of an HTML document in Hypertext with the DSL:

```
Hypertext::DSL.new do
  html lang: "en-GB" do
    head do
      title do
        text "Hello, World!"
      end
    end
    body do
      h1 do
        text "Welcome"
      end
    end
  end
end
```

Let’s pull out the block passed to the DSL and put it into a template file, `index.ht`:

```
html lang: "en-GB" do
  head do
    title do
      text "Hello, World!"
    end
  end
  body do
    h1 do
      text "Welcome"
    end
  end
end
```

And then let’s see if we can generate the HTML output from a Ruby file:

```
require "hypertext"
require "hypertext/dsl"

def fragment(template)
  instance_eval <<-CODE
    Hypertext::DSL.new do
      #{File.read(template)}
    end.to_s
  CODE
end

puts fragment("index.ht")
```

And the result?

```
<html lang="en-GB">
  <head>
    <title>
      Hello, World!
    </title>
  </head>
  <body>
    <h1>
      Welcome
    </h1>
  </body>
</html>
```

Happy days. We have an initial, working proof of concept. What if we now want to expose variables to the template?

I think, similar to what I have done with [Sew][sew] we can wrap the above code in a context class and ensure that the variables are accessible in the context, and that the template is evaluated in that same context.

```
require "hypertext"
require "hypertext/dsl"

class Context
  attr_reader :params
  
  def initialize(params)
    @params = params
  end
  
  def render(template)
    instance_eval <<-CODE
      Hypertext::DSL.new do
        #{File.read(template)}
      end.to_s
    CODE
  end
end
```

This should allow us, assuming `params` is a hash with symbol keys, to access variables like a title, via `params[:title]`.

```diff
  html lang: "en-GB" do
    head do
      title do
-       text "Hello, World!"
+       text params[:title]
      end
    end
    …
  end
```

And then putting it all together by actually executing the render method with the params and the template:

```
require_relative "./context"

puts Context.new({ title: "Hello, World!" }).render("index.ht")
```

Unfortunately this blows up.

```
undefined local variable or method `params' for #<Hypertext::DSL:0x00007fd0188ef040> (NameError)
```

`params` isn’t available to the Hypertext document definition. I think this is due to a case of nested `instance_eval`s, but I’m not sure.

If we modify the render method:

```diff
    def render(template)
      instance_eval <<-CODE
+       params = @params
        Hypertext::DSL.new do
          #{File.read(template)}
        end.to_s
      CODE
    end
```

then we have more luck, this time, rendering the HTML flawlessly with the interpolated variable.

If we continue down this road we could neaten it up a little, removing the need for `params[:title]` and instead making it more Rails-like by using `Object#instance_variable_set` and exposing `@title`.

```diff
    def render(template)
      instance_eval <<-CODE
        params = @params
        Hypertext::DSL.new do
+         params.each do |key, val|
+           next if [:ht, :dom].include?(key)
+           instance_variable_set(:"@#{key}", val)
+         end
          #{File.read(template)}
        end.to_s
      CODE
    end
```

We’ll have to update the template from calling `params[:title]` to `@title`.

That does the job. Notice that I’ve protected the DSL instance variables `@ht` and `@dom` from being overwritten. Perhaps there are cleaner ways to expose variables to the template.

In a future post I’ll explore whether or not it is possible to expose helper methods to the DSL.

—*Wednesday 24th March 2021.*

[hte]: https://www.crossingtheruby.com/2021/03/22/hypertext-dsl-templating-explorations.html
[meis]: https://www.crossingtheruby.com/2021/03/03/templating-with-markaby-in-syro.html
[sew]: https://www.crossingtheruby.com/2021/02/22/rendering-with-mote-in-sew.html
