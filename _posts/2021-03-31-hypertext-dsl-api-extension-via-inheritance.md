---
layout: post
title: "Hypertext DSL: API Extension via Inheritance"
---

At the end of a previous post applying the [Markaby variables and helpers API][mvh] to Hypertext I asked the question:

> Are there other, simpler, easier, more fitting APIs? Is there a way to accomplish the delegation of unknown methods to the helper without method_missing?

What I love about doing a lot of this API exploration out in the open is that releasing an imperfect solution into the world does at least two things: it frees up your mind to move beyond the point at which you currently stand; and it invites others into the process, often involving minds much smarter and more experienced than my own.

That latter scenario was the case today when I received a Gist from Mr. Martens showcasing a blindly obvious way to introducing variables and helpers into Hypertext without at all modifying the `Hypertext::DSL` class. Sometimes you’re so busy looking for something that you don’t realise it’s staring you right in the face.

Rather than complicated instance variable partitioning, or relying on meta-programming ‘magic’ we can use one of the tried and trusted tools of Ruby’s object oriented heritage, inheritance.

That is, we can create a context object by inheriting from the `Hypertext::DSL` class:

```
class Context < Hypertext::DSL
end
```

Instead of patching the `Hypertext::DSL#initialize` method, we can simply extend it in the sub class and introduce the variable assignment identically to how we have been doing it from the start, using `instance_variable_set`. 

```
class Context < Hypertext::DSL
  def initialize(params, &block)
    params.each do |key, val|
      instance_variable_set(sprintf("@%s", key), val)
    end

    super(&block)
  end
end
```

If we want helper methods, then we can define them in this context class:

```
class Context < Hypertext::DSL
  …
  
  def id_generator
    "id-#{rand(100)}"
  end
end
```

or we can give birth to a ‘grandchild’:

```
class Template < Context
  def id_generator
    "id-#{rand(100)}"
  end
end
```

If we use this last approach, then a full example would look similar to the following:

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

html = Template.new page_title: "Hello, World!" do
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
end

puts html.to_s

# =>
# <html lang="en-GB">
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <h1 id="id-60">
#       Welcome
#     </h1>
#   </body>
# </html>
```

No magic. No complications. Just straightforward inheritance. So simple and obvious it never even occurred to me.

Thank you, Mr. Martens, for opening my eyes. Again.

—*Wednesday 31st March 2021.*

[mvh]: https://www.crossingtheruby.com/2021/03/30/hypertext-dsl-markaby-variables-and-helpers-api.html
