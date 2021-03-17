---
layout: post
title: Hypertext Comparison
---

In previous posts I [introduced][ht1] Michel Marten’s [Hypertext library][ht], shed light on its [genesis][htg], and examined its [internals][hti]. In this post I’d like to compare Hypertext with my small [HTML helper library][hhl].

Now one thing that is important not to overlook before we start examining some of the differences between the two libraries is how incredibly similar they are. Again, these were developed independently of one another, so for both to come out so clean in the wash I think is a testament to simplicity.

```
# HTMLDocument
doc = HTMLDocument.new do |d|
  d.el :html, lang: "en-GB" do
    d.el :head do
      d.el :title do
        d.text "Hello, World!"
      end
    end
  end
end

#=> <html lang="en-GB"><head><title>Hello, World!"</title></head></html>

# Hypertext
html = Hypertext.new do |h|
  h.tag :html, lang: "en-GB" do
    h.tag :head do
      h.tag :title do
        d.text "Hello, World!"
      end
    end
  end
end

#=>
<html lang="en-GB">
  <head>
    <title>
      Hello, World!
    </title>
  </head>
</html>
```

It also highlights the strong sense of taste and style that Rubyists gravitate towards, achieving more of a consistency than perhaps in other languages. In Ruby the gauntlet has been laid down on account of the language’s high-level elegance and a good workman attempts to work with that grain rather than against it.

This is also the motivation that passed over the simpler and more functional style of Elm and Elixir, which simply nests children as arguments, and led to the decision to use Ruby blocks. It is more idiomatic.

Apart from library, class and method naming, the first big difference you can see in the examples above is that Hypertext crafts the HTML much more carefully with the presence of indentation, mirroring the nesting of tags and text. HTMLDocument just outputs a concatenated string with no newlines and thus no indents. Although HTMLDocument might win the race in a straight web performance speed fight, I much prefer Hypertext’s meticulous approach. 

(It even offers the possibility for the waging of internal flame wars over indentation levels, I’ll leave it up to you to decide whether or not that’s a positive or a negative.)

The naming also deserves comment. Like all of Mr. Martens libraries, it is obvious that as much thought has gone into the naming as into the code itself. It might even be appropriate to say that for the diligent workman there is no distinction. Naming _is_ coding. Encoding.

Hypertext is a wonderful name, for that is precisely what you are doing: producing _hypertext_ in HyperText Markup Language (HTML). My working title of `HTMLDocument` gives you, as they say here in the Netherlands, “a mouthful of teeth”, or as I would say, it doesn’t exactly roll off the tongue. 

For the connoisseurs among us, there is one important semantic distinction which makes Hypertext a far better name. Hypertext does not limit you to a document but allows you to create document fragments without introducing a naming inconsistency. A document fragment remains hypertext, but a document fragment does not equal an HTML document.

I don’t know if you’re keeping score, but that’s no points so far for HTMLDocument.

I also like the use of `h.tag` over and above `d.el`. Although the latter is shorter, it looks far too much like del-ete when we want instead to be creating. The tokens `h` for _html_ and `tag` for _tag_ are also more faithful to the underlying nomenclature. It fits.

If we move deeper into the code from the surface this is where the similarity is strong. The API for the `tag`/`el` method is identical:

```
def el(type, attrs={})
def tag(name, attributes = {})
```

Both use an instance variable to accumulate a representation of the final HTML, `@dom` and `@doc`. Although a key difference here is that `@dom` is an array structure containing a simple abstract syntax tree (AST) whereas `@doc` generates the final HTML string on the fly. HTMLDocument therefore has no need for any rendering logic, whereas Hypertext requires a `render` method. They do both expose the same `to_s` API, where HTMLDocument simply returns the contents of `@doc` while Hypertext passes `@dom` to `render`.

This decision definitely has trade-offs. HTMLDocument’s ‘instant’ HTML generation makes for a simpler library. `el` is five lines long and easy to understand. `tag` is double the length at ten lines and it took me a few moments to get my head around the logic.

```
class HTMLDocument
  …
  def el(type, attrs={})
    @doc << opening_tag(type, attrs)

    unless VOID_ELEMENTS.include? type
      yield
      @doc << closing_tag(type)
    end
  end
  …
end

class Hypertext
  …
  def tag(name, attributes = {})
    atts = compile(attributes)

    if block_given?
      @dom << "<#{name}#{atts}>"

      original, @dom = @dom, []
      yield
      @dom = original << @dom

      @dom << "</#{name}>"
    else
      @dom << "<#{name}#{atts} />"
    end
  end
  …
end
```

On the other hand, although I could add some indentation to HTMLDocument without too much bother, storing the document structure in a simple AST allows you to cleanly separate the rendering logic from the accumulation logic. It also offers an easy way to configure preferences outside of that logic rather than polluting it and mixing concerns. It’s really a six and two threes depending largely on how important you deem that separation.

Whereas HTMLDocument relies on a static definition of HTML void elements, which ties it in to a specific implementation of HTML, Hypertext chooses a nice route, simply relying on the presence or absence of a nested block to decide whether or not to add a closing tag or a self-closing tag. I like this design decision a lot because it makes the library antifragile.

```
class HTMLDocument
  VOID_ELEMENTS = %i[
    area base br col embed hr img input 
    link meta param source track wbr
  ]
  …
  def el(type, attrs={})
    …
    unless VOID_ELEMENTS.include? type
    …
  end
  …
end

class Hypertext
  …
  def tag(name, attributes = {})
    …
    if block_given?
    …
  end
  …
end
```

HTML attribute generation is almost too similar to warrant further comment, but Hypertext does cater for valueless HTML attributes like `disabled` whereas HTMLDocument does not. This is one of two key considerations that make Hypertext production ready, over and above HTMLDocument.

The second is the escaping of XML predefined entities in both attribute values and text content:

```
# HTMLDocument
def text(string)
  @doc << string
end

# Hypertext
def text(value)
  @dom << escape(value)
end
```

As mentioned in [Hypertext Innards][hti] I really like the `gsub` Hash trick in the `escape` method, the string interpolation, and the attribute spacing trick. Hypertext also obviates the need to have a check for empty attributes, relying on the `nil` return from `Enumerable#map` and the spacing trick to simply print the output of the attribute method in all cases.

Last, but certainly not least, in view of the reduced functionality in HTMLDocument, it comes in at 38 SLOC to Hypertext’s 84 SLOC. I’m very happy about that, and I’d be interested to figure out if I could match the functionality and keep it smaller. But that’s an exercise for another day.

This has been a tremendously fruitful exercise where I’ve learned a lot. I’m happy that I trusted my gut, kept my eyes of Hypertext, and ploughed on with HTMLDocument. The result is that I’m pleasantly surprised at how far I have developed my workmanship under the tutelage of a master craftsman.

—*Wednesday 17th March 2021.*

[ht1]: https://www.crossingtheruby.com/2021/03/14/hypertext-write-html-in-ruby.html
[ht]: https://github.com/soveran/hypertext
[htg]: https://www.crossingtheruby.com/2021/03/15/hypertext-origins.html
[hti]: https://www.crossingtheruby.com/2021/03/16/hypertext-innards.html
[hhl]: https://gist.github.com/pootsbook/c3869f089af6fb1cf1fd53bc0250961b
