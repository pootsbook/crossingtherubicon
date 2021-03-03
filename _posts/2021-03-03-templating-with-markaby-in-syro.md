---
layout: post
title: Templating with Markaby in Syro
---

There’s something about [Elm’s HTML][ehl] DSL that means I will never be satisfied with writing views or templates in a separate templating library. I think it is the lack of context switching where everything in your application is written in the same language.

```
view =
  div [ class "container" ] 
    [ h1 [ ] [ "Title" ]
    , p [ ] [ "Hello, World!" ]
    ]
```

It’s also a good example of a faithful abstraction, mirroring HTML tags and attributes with the minimum of fuss.

In Elixir, although José Valim recommends [not going down this route][jvr] (I’m not sure why), there is the [Temple library by Mitchell Hanberg][temple] which I am watching closely. 

In Ruby, we can turn to a very old library from why the lucky stiff<sup>1</sup> called [Markaby][markaby].

Markaby allows you to write HTML in Ruby as follows:

```
html do
  head do
    title "Title"
  end

  body do
    div.container do
      h1 "Title"
      p "Hello, World!"
    end
  end
end
```

This is passed to a `Markaby::Builder` object along with some parameters and a context for the template to be executed in, and then it evaluates the code, outputting it to a string.


So let’s test my assertion that Syro is just a routing library and could be integrated with other templating libraries at will. This is what rendering a template in Syro with Markaby would look like:

```
App = Syro.new(Web) do 
  get do
    page_title = "Title"
    content = "Hello, World!"
    
    html = Markaby::Builder.new do
      html do                                                                             
        title { page_title }                                                             
        body do                                                                           
          h1 { page_title }
          p { content }
        end                                                                               
      end                                                                                 
    end.to_s                                                                              

    res.html html                                                                         
  end
end
```

Since the builder shares the same context as the local variables they are accessible from the template. Another option would be to specify them explicitly and then reference them as instance variables:

```
App = Syro.new(Web) do 
  get do
    page_title = "Title"
    content = "Hello, World!"
    
    html = Markaby::Builder.new(page_title: page_title, content: content) do
      html do                                                                             
        title { @page_title }                                                             
        body do                                                                           
          h1 { @page_title }
          p { @content }
        end                                                                               
      end                                                                                 
    end.to_s                                                                              

    res.html html                                                                         
  end
end
```

Now doing this for each route would soon create a beast of a routes file so we can split each view off into its own template:

```
# views/index.mab

html do
  title { @page_title }
  body do
    h1 { @page_title }
    p { @content }
  end
end
```

And then write a Syro Deck for rendering:

```
class Web < Syro::Deck
  def view(template)
    File.read("views/#{template}.mab")
  end

  def partial(template, params = {})
    instance_eval <<-CODE
      Markaby::Builder.new(params, self) do
        #{view(template)}
      end.to_s
    CODE
  end
end
```

This mirrors our code for Mote closely with the only main difference being the actual rendering logic. I picked up the tip for evaluating the code as a string as a means of piecing together the boilerplate and the template file from the [Markaby Rails helper][mrh].

In this scenario we pass in the parameters explicitly and ensure that the code is evaluated in the context of the `Syro::Deck` making the relevant methods available to it.

Layouts can also be accomplished using the double render trick that we highlighted previously and indeed this is the first example on the [Markaby home page][markaby] and needs no repeating here.

Markaby is in no way as minimal as Mote but should you prefer the Ruby HTML DSL then it offers a nice alternative. Syro offers the flexibility to piece together the parts that you need and integrate them easily. 

—*Wednesday 3rd March 2021.*

[ehl]: https://package.elm-lang.org/packages/elm/html/latest/
[jvr]: https://news.ycombinator.com/item?id=25781067
[temple]: https://www.mitchellhanberg.com/introducing-temple-an-elegant-html-library-for-elixir-and-phoenix/
[markaby]: http://markaby.github.io
[mrh]: https://github.com/markaby/markaby/blob/master/lib/markaby/rails.rb

<div class="footnotes">
  <ol>
    <li>_why is the person who made the Ruby community such a fun and magical place in it’s early days. He is what made Ruby <em>different</em>. It’s no coincidence that his departure and the commercialisation of Ruby follow roughly the same timeframe.</li>
  </ol>
</div>
