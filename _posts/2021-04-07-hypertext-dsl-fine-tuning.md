---
layout: post
title: "Hypertext DSL: Fine-Tuning"
---

Yesterday I arrived at an API for [rendering templates][rth] with Hypertext DSL that I was happy with.

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
```

I thought of a couple of minor optimisations to extend the scope and make it a little more robust.

The first is to extend the scope. Say we wanted to pass a Hypertext DSL string for rendering rather than the name of a file. Instead of loading a string from file, we would hand the string to the `Context` on initialization. 

In principle we can elevate a parameter specifically for this purpose, similar to `template` and `layout`, we’ll call it `fragment` (We can argue about the naming another time).

If a fragment is passed in, we’ll favour that above the template which leads to the following modification in the initialize method:

```
def initialize(params)
  params.each do |key, val|
    instance_variable_set(sprintf("@%s", key), val)
  end

  @ht = Hypertext.new
  if @fragment
    fragment @fragment
  else
    partial @layout
  end
end
```

We’re referencing a `fragment` method that we haven’t written yet. This becomes:

```
def fragment(string)
  instance_eval string
end
```

If we squint a little, we see that this is almost identical to our `partial` method which we can now modify to take account of `fragment`’s existence.

```
def partial(filename)
  fragment File.read(filename)
end
```

Our class is now set up to deal with both reading from file and dealing with templates passed as strings.

To resume the naming discussion, we could do several different things to make it more explicit. What about the following?

```
template
template_string  # formerly 'fragment'

layout
layout_string
```

If we do this, both giving each a clear name, and allowing a combination, for example, providing a layout as a string and a template from a file then we also need to make the code a little more robust.

What if we don’t have a layout?

```
def initialize(params)
  params.each do |key, val|
    instance_variable_set(sprintf("@%s", key), val)
  end

  @ht = Hypertext.new
  if @layout_string
    fragment @layout_string
  elsif @template_string
    fragment @template_string
  elsif @layout
    partial @layout
  else
    partial @template
  end
end
```

It’s a little ugly. Maybe we should just settle on one approach, either loading from file or providing a string. The code is so easy to change anyway that it doesn’t make sense to build in this cyclomatic complexity. We’ll also run into issues if we try and combine approaches. The layout will have to know to either call `partial` or `fragment` with either `@template` or `@template_string` respectively.

This is maybe where trying to be all things to all men suddenly introduces a level of complexity that outweighs the simplicity of the problem. If it’s so straightforward to rewrite the code for your use-case, then, to me at least, it doesn’t make sense to burden the tool with a number of cases from which you will likely only use one branch in a given scenario.

The presence or absence of a layout does seem to make sense though, so we can bake that in:

```
  @ht = Hypertext.new
  if @layout
    partial @layout
  else
    partial @template
  end
```

or simpler:

```
partial(@layout || @template)
```

If we were to go down the fragment route it would be similar:

```
fragment(@layout_string) || @template_string)
```

Do you agree that it’s better to focus on one way? Or should code like this provide the flexibility? 

Maybe we could do something ‘smart’ like having a method that sits above `partial` and `fragment` and works out whether the string which has been passed in is a file in the file system or not and then delegates to the appropriate method based on the result?

```
def partial(val)
  if File.exist?(val)
    partial_from_file(val)
  else
    fragment val
  end 
end

def partial_from_file(filename)
  fragment File.read(filename)
end

def fragment(string)
  instance_eval string
end
```

That looks ripe for refactoring:

```
def partial(val)
  if File.exist?(val)
    fragment File.read(val)
  else
    fragment val
  end
end

def fragment(string)
  instance_eval string
end
```

If we really wanted to we could make it recursive and fold it into one method:

```
def partial(val)
  if File.exist?(val)
    partial File.read(val)
  else
    instance_eval val
  end
end
```

I’m not sure how I feel about that route. It doesn’t look too bad on the surface but I’m not fully sold. It would allow us to pass in `template` and `layout` params without worrying whether they are strings or file names. I think I’m going to sleep on it.

What do you think? Spot any edge-cases that this approach might create?

—*Wednesday 7th April 2021.*

[rth]: https://www.crossingtheruby.com/2021/04/06/hypertext-dsl-reading-nested-templates-from-file-2.html
