---
layout: post
title: "Hypertext DSL: Reading Templates from File"
---

We proved that [nested templates][nt] were possible by either instantiating multiple instances of a `Hypertext::DSL` class and combining them or by modifying the subclass to render layout, template and partial.

In this post I want to see what nested templates might look like when loading fragments from a file versus defining them in the DSL subclass.

As a reminder we are starting off with this code as the base:

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

For starters, let’s change the `Context#initialize` method to accept a string instead of a block. That way we can pass in the contents of a file.

```diff
  class Context < Hypertext::DSL
-   def initialize(params, &block)
+   def initialize(params, fragment)
      params.each do |key, val|
        instance_variable_set(sprintf("@%s", key), val)
      end
  
-     super(&block)
+     super(fragment)
    end
  end
```

And let’s test that out with a basic template:

```
Template.new({}, 'text "Hello, World"').to_s
```

Now `Hypertext::DSL#initialize` expects a block so that’s not going to cut the mustard. Let’s do some surgery to be able to continue down this path.

```
class Hypertext
  class DSL
    def initialize(string)
      @ht = Hypertext.new
      instance_eval <<-CODE
        #{string}
      CODE
    end
  end
end
```

Now that’s a little extreme, so rather than monkey patch the class, we can see that the only thing we need to do is to set the instance variable. Let’s do that in our `Context` class instead.

```diff
  class Context < Hypertext::DSL
    def initialize(params, fragment)
      params.each do |key, val|
        instance_variable_set(sprintf("@%s", key), val)
      end
  
-     super(fragment)
+     @ht = Hypertext.new
+     instance_eval fragment
    end
  end
```

Now, if we create a template using a string instead of a block we can check to see whether or not this works:

```
puts Template.new({}, 'text "Hello, World!"').to_s

# =>
# Hello, World!
```

Looking good. Let’s try with a template file as follows:

```
# index.ht

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

And loading that in:

```
params = { page_title: "Hello, World!" }
template = Template.new(params, File.read("index.ht"))
puts template.to_s
```

Gives us the following result:

```
# =>
# <html lang="en-GB">
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <h1 id="id-15">
#       Welcome
#     </h1>
#   </body>
# </html>
```

That’s looking good. 

And that’s all I’ve got time for today. We’re reading the template in from a file and successfully executing it in the right context with both helper methods and variables. In a future post we can explore both layouts and partials loaded from file in a similar manner and how they fit together.

—*Sunday 4th April 2021.*

[nt]: https://www.crossingtheruby.com/2021/04/03/hypertext-dsl-nested-templating.html
