---
layout: post
title: "Hypertext DSL: Reading (Nested) Templates from File"
---

In a [previous post][thd] we read a template written in the Hypertext DSL from file and successfully executed it in our context with variables and helpers.

In this post I want to continue down that path and see if we can integrate reading a layout and partials from file and then compile them together with the template. We can use our previous experiments in [nested templating][nth] with Hypertext as a guide while using our initial code (from [yesterday][thd]) as a starting point:

```
class Context < Hypertext::DSL
  def initialize(params, fragment)
    params.each do |key, val|
      instance_variable_set(sprintf("@%s", key), val)
    end

    @ht = Hypertext.new
    instance_eval fragment
  end
end

class Template < Context
  def id_generator
    "id-#{rand(100)}"
  end
end
```

We could of course mimic the approach with Sew and Syro with a double pass and two `Template` classes. Assuming the following layout and template (no partials):

```
# layout.ht

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

# template.ht

p do
  text "A Hypertext template."
end
```

Then applying this approach as before, but instead loading everything in from file:

```
params = { page_title: "Hello, World!" }
template = Template.new(params, File.read("template.ht"))
layout = Template.new(
  params.merge(content: template.to_s),
  File.read("layout.ht"))
puts layout.to_s
```

This gives us the desired output, with the template nested inside the layout and rendering the HTML.

```
# =>
# <html lang="en-GB">
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <p>
#   A Hypertext template.
# </p>
# 
#   </body>
# </html>
```

Before I explore a way to render the layout in the same `Template` class let’s take a look at partials. Instead of calling a helper method which contains the hypertext definition as before:

```
class Template < Context
  …
  def heading
    h1 id: id_generator do
      text "Welcome"
    end
  end
end
```

we’ll move this out into a file:

```
# heading.ht (partial)

h1 id: id_generator do
  text: "Welcome"
end
```

and then we need to have a helper method which takes a filename and evaluates the contents of the file, adding it to the AST.

```
class Template < Context
  …
  def partial(filename)
    instance_eval File.read(filename)
  end
end
```

We can then update our `template.ht` file to call this partial with the header:

```
partial "header.ht"                                      
p do                                                     
  text "A Hypertext template."                           
end
```

And now, leaving the layout aside for one moment, we have a template which renders a fragment from file in the current context:

```
params = { page_title: "Hello, World!" }
template = Template.new(params, File.read("template.ht"))
puts template.to_s

# =>
# <h1 id="id-75">
#   Welcome
# </h1>
# <p>
#   A Hypertext template.
# </p>
```

This approach to partials feels really clean. We’re essentially applying the same trick for both the template and the partial using `instance_eval` with the template definition string, loading it from file. This is different to how we put the layout and the template together with two different instances of a `Template` class and piecing them together via the `params` hash and `append`.

In a future post I’d like to explore if we can have “turtles all the way down” and integrate the layout, template and partial using the same technique.

—*Monday 5th April 2021.*

[thd]: https://www.crossingtheruby.com/2021/04/04/hypertext-dsl-reading-templates-from-file.html
[nth]: https://www.crossingtheruby.com/2021/04/03/hypertext-dsl-nested-templating.html
