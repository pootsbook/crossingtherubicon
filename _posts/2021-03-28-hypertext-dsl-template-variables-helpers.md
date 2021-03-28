---
layout: post
title: "Hypertext DSL: Template Variables and Helpers"
---

In previous posts we established proofs of concept for integrating both [variable interpolation][hvi] in templates and mixing in [helper methods][hhm] to the Hypertext DSL template context. However, we proved the ability to accomplish these two things independently of one another.

I’d like to figure out if we can bring both variable interpolation and helper methods together within the Hypertext DSL.

I’ll begin with the most recent proof, the solution for _helper methods_. This involves moving the DSL out to a module and including this module into a context class, so that instances of the class have access to both the helper methods (defined in the context) and the DSL methods (defined in the included module).

To jog our memory we can consider the `render` method which takes our `Context` and evaluates a template definition:

```
def render(template)
  instance_eval <<-CODE
    Context.new do
      #{File.read(template)}
    end.to_s
  CODE
end
```

`Context` is a class mixing in the DSL and including definitions of any desired helper methods:

```
class Context
  include DSL

  def id_generator
    "id-#{rand(100)}"
  end
end
```

For _variable interpolation_ our render method had the following implementation:

```
def render(template)
  instance_eval <<-CODE
    params = @params
    Hypertext::DSL.new do
      params.each do |key, val|
        next if [:ht, :dom].include?(key)
        instance_variable_set(:"@#{key}", val)
      end
      #{File.read(template)}
    end.to_s
  CODE
end
```

The `@params` is evidence that this method was part of a class that contained a reference to the parameters necessary for variable interpolation. We iterated over this Hash in the DSL context and dynamically set an instance variable for each key-value pair. For example, a Hash with value `{ title: "Hello, World!"}` would allow the template to reference the _title_ via an instance variable carrying the same name as the key in the Hash, `@title`.

Let’s shed the class and pass the template parameters as method parameters to the `render` method:

```
def render(template, params)
  …
end
```

Now we need to alter the rest of the method to match our desire to use the `Context` with a mixed in DSL rather than the DSL directly:

```
def render(template, params)
  instance_eval <<-CODE
    Context.new do
      params.each do |key, val|
        next if [:ht, :dom].include?(key)
        var = sprintf("@%s", key)
        instance_variable_set(var, val))
      end
      #{File.read(template)}
    end.to_s
  CODE
end
```

And there we have both helper methods and interpolation working together. At least that’s what I think having read the code. What if I let the computer read the code with the following `index.ht` file?

```
html lang: "en-GB" do
  head do
    title do
      text @page_title
    end
  end
  body do
    h1 id: id_generator do
      text "Welcome"
    end
  end
end
```

The computer confirms that both are working together in harmony by producing the following output:

```
<html lang="en-GB">
  <head>
    <title>
      Hello, World!
    </title>
  </head>
  <body>
    <h1 id="id-95">
      Welcome
    </h1>
  </body>
</html>
```

We see the variable `@title` has been replaced by the value passed in via parameter `Hello, World!`. The value of the `id` attribute on the `h1` element has been computed by the `Context#id_generator` method and integrated into the template’s output.

_I made one change because for some reason in this construction when looping through the Hash the `key` variable wasn’t being recognized when interpolated into the string. I replaced it with a call to `sprintf`._

```diff
  def render(template)
    instance_eval <<-CODE
      Context.new do
        params.each do |key, val|
          next if [:ht, :dom].include?(key)
-         instance_variable_set(:"@#{key}", val)
+         var = sprintf("@%s", key)
+         instance_variable_set(var, val)
        end
        #{File.read(template)}
      end.to_s
    CODE
  end
```

So rather than two independent proofs of concept we now have one integrated proof of concept which demonstrates both variable interpolation and helper methods in Hypertext. I’m still not sure whether this is the appropriate implementation or API but it is something to start with. I’d like to take it for a spin with more involved examples to see how this approach stands up.

—*Sunday 28th March 2021.*

[hvi]: https://www.crossingtheruby.com/2021/03/24/hypertext-dsl-template-context-variables.html
[hhm]: https://www.crossingtheruby.com/2021/03/27/hypertext-dsl-template-context-helpers-2.html
