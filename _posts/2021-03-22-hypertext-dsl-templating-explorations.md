---
layout: post
title: "Hypertext DSL: Templating Explorations"
---

Hypertext has a [DSL][dsl] with a [simple implementation][dsi]. Until now I have only played with basic examples. In this post I want to see what it might look like to use Hypertext’s DSL to implement a basic templating system.

A basic templating system should be able to handle a global layout, template definition and fragments, often called partials. Given Hypertext allows us to remain in the world of Ruby versus a dedicated templating language with it’s own syntax, we don’t have to worry much about variable interpolation or anything of that sort.

Let’s define a template which makes use of a `page_title` variable in addition to the static content.

```
page_title = "Hello, World!"

content = Hypertext::DSL.new do
  div class: 'content' do
    h1 do
      text page_title
    end
    p do
      text "Welcome to a page created in "
      a href: "https://github.com/soveran/hypertext" do
        text "Hypertext"
      end
      text "."
    end
  end
end.to_s
```

Let’s then define a layout, which will use two variables, `page_title` and `content`, where `content` is what will eventually hold the template.

```
require "hypertext"
require "hypertext/dsl"

layout = Hypertext::DSL.new do
  html lang: "en-GB" do
    head do
      title do
        text page_title
      end
    end
    body do
      div class: 'container' do
        content
      end
    end
  end
end.to_s
```

And if we print `layout`:

```
puts layout

# <html lang="en-GB">
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <div class="container">
#       &lt;div class=&quot;content&quot;&gt;
#   &lt;h1&gt;
#     Hello, World!
#   &lt;/h1&gt;
#   &lt;p&gt;
#     Welcome to a page created in 
#     &lt;a href=&quot;https://github.com/soveran/hypertext&quot;&gt;
#     &lt;/a&gt;
#     .
#   &lt;/p&gt;
# &lt;/div&gt;
# 
#     </div>
#   </body>
# </html>
```

Hmm. That doesn’t look quite right. The outer layout is fine, but the inner template, interpolated via `content` looks like gobbledygook. If we look a little closer we can discern that the HTML from content is being escaped according to the rules defined in Hypertext’s `escape` method.

Now to be fair, Mr. Martens had pointed out to me the potential of this happening in an email. He suggested two alternative solutions. One was to do something funky with the AST and the other, much more straightforward, option was to write an `append` method that avoids escaping the nested Hypertext DSL fragment. 

Let’s monkey patch Hypertext and the DSL at the top of our file:

```
require "hypertext"
require "hypertext/dsl"

class Hypertext
  def append(fragment)
    @dom << fragment
  end

  class DSL
    def append(fragment)
      @ht.append(fragment)
    end
  end
end
```

As you can see, just like for the `text` method, the DSL method is simply a wrapper for the identically named Hypertext method and the implementation is identical to text with the exception of passing the value to `escape`. Here, we simply add the fragment (which is a Hypertext AST) to the `@dom` instance variable. I expect the indentation will be off, but I’m not too worried about that right now.

If we re-run the script then we get the following output:

```
puts layout

# <html lang="en-GB">
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <div class="container">
#       <div class="content">
#   <h1>
#     Hello, World!
#   </h1>
#   <p>
#     Welcome to a page created in 
#     <a href="https://github.com/soveran/hypertext">
#       Hypertext
#     </a>
#     .
#   </p>
# </div>
# 
#     </div>
#   </body>
# </html>
```

Indeed, the indentation is skewed, but if we look on the bright side, `content` is indeed rendered in HTML without having been escaped.

In principle, append should work for partials too, let’s define one:

```
partial = Hypertext::DSL.new do
  footer do
    p do
      text "Stop footering with the footer."
    end
  end
end
```

If we make sure to define `partial` first, then `content` and then `layout` and output the layout again:

```
puts layout

# <html lang="en-GB">
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <div class="container">
#       <div class="content">
#   <h1>
#     Hello, World!
#   </h1>
#   <p>
#     Welcome to a page created in 
#     <a href="https://github.com/soveran/hypertext">
#       Hypertext
#     </a>
#     .
#   </p>
# </div>
# <footer>
#   <p>
#     Stop footering with the footer.
#   </p>
# </footer>
# 
# 
#     </div>
#   </body>
# </html>
```

Our footer is appended to the document as expected. I’ve given these HTML document fragments the names `layout`, `content` and `partial` simply to match the templating terminology, but we could do a couple of things to improve. One is to give the variables more descriptive names (which I’ll leave out for now), and the other is to move the fragment definitions into methods:

```
def layout
  Hypertext::DSL.new do
    … 
  end.to_s
end
```

If we try and run that then we get an error message:

```
undefined local variable or method `page_title' for #<Hypertext::DSL:0x00007ffa0297f190> (NameError)
```

So the variables that we have defined are not available inside the method. Let’s pass the necessary variables into the layout:

```
def layout(page_title, content)
  …
end

puts layout(page_title, content)
```

And we’re back to where we were with a functional template. If we also pull `template` and `partial` into methods:

```
def partial
  Hypertext::DSL do
    footer do
      …
    end
  end
end

def content(page_title)
  Hypertext::DSL.new do
    div class: 'content' do
      …
    end
  end
end
```

We could then rename `content` to `home_page` and `partial` to `footer` and we’re looking a lot more like a templating system. I think there is a better way to make variables accessible to the fragments. I’ll have to think about that a little more.

Nevertheless, I’m satisfied with this proof of concept which proves that, at least in principle, we can put some kind of templating system together with Hypertext. What do you think? Do you see any improvements or alternative approaches?

—*Monday 22nd March 2021.*

[dsl]: https://www.crossingtheruby.com/2021/03/20/hypertext-dsl.html
[dsi]: https://www.crossingtheruby.com/2021/03/21/hypertext-dsl-implementation.html

<div class="references">
  <h3>References</h3>
  <ul>
    <li><a href="https://gist.github.com/pootsbook/0de09f2697e1378c9b9ef2d61fa40f97">Gist</a> of script with output.</li>
  </ul>
</div>
