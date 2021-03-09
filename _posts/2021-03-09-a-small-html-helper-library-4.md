---
layout: post
title: A Small HTML Helper Library (4)
---

Over the [last][shl1] [few][shl2] [days][shl3] I have been exploring a way to write a small library (script?) that would give me a nice Ruby DSL for writing HTML documents.

The inspiration comes from the [Elm HTML library][elm] which I believe forms a key part of Elm’s delightful usability as a language for the front-end. For me, the main draw is avoiding the context switch between the programming language and a templating language.

There are side benefits, like having a thin and faithful abstraction layer over the underlying HTML document, but the primary benefit is being able to deal with only one programming model in your head.

The goal was to arrive at something like this:

```
HTMLDocument.new do |d|
  d.el :html, lang: "en-GB" do
    d.el :head do
      d.el :title do
        d.el :text do
          "Hello, World!"
        end
      end
    end
  end
end.to_s
```

It is in many ways also inspired by [Markaby][mkby] but eschews the magic (e.g. special syntax for class and id definition) in favour of a more faithful modelling of HTML tags and attributes.

And of course, the desire for simplicity is inspired by that which I have seen in the Ruby libraries written by [Michel Martens][mm].

I did manage to arrive at a solution for generating HTML from a document written in the syntax above. However, I was very unhappy with the approach I took.

Nevertheless, sometimes making it work is the first step. Then you can make it beautiful.

There are serious benefits to sleeping on problems. A couple of days ago I resigned myself to what felt like defeat. The next morning I broke through the last few barriers to arrive at a solution.

Today, in a flash of inspiration, I ripped out most of the code that I wrote yesterday based on a growing hunch:

> The nested block “grandparent” wrapping I stumbled upon makes me think we can generate the HTML string on the fly instead of some flattened tree or AST. It would greatly reduce the surface area of the code and get rid of the need for an underlying data abstraction completely. I’d like to try that next.

So this is what I ended up with in just a couple of minutes:

```
class HTMLDocument
  def initialize
    @doc = ""

    yield self if block_given?
  end

  def el(type, attrs={})
    case type
    when :text
      @doc << yield
    when :input
      @doc << opening_tag(type, attrs)
    else
      @doc << opening_tag(type, attrs)
      yield
      @doc << closing_tag(type)
    end
  end

  def opening_tag(name, attrs)
    if attrs.empty?
      "<#{name}>"
    else
      "<#{name} #{attributes(attrs)}>"
    end
  end

  def closing_tag(name)
    "</#{name}>"
  end

  def attributes(attrs)
    attrs.map do |key, value|
      %Q(#{key}="#{value}")
    end.join(" ")
  end

  def to_s
    @doc
  end
end
```

That’s it. From 75 down to 36 significant lines of code. No data structures, no complicated tree surgery, just some straightforward string concatenation. It doesn’t even need any explanation.

I think it’s simple. I think it’s beautiful. 

What do you think?

—*Tuesday 9th March 2021.*

[shl1]: https://www.crossingtheruby.com/2021/03/06/a-small-html-helper-library.html
[shl2]: https://www.crossingtheruby.com/2021/03/07/a-small-html-helper-library-2.html
[shl3]: https://www.crossingtheruby.com/2021/03/08/a-small-html-helper-library-3.html
[elm]: https://package.elm-lang.org/packages/elm/html/latest/
[mkby]: http://markaby.github.io
[mm]: https://github.com/soveran?tab=repositories&q=&type=&language=ruby

<div class="references">
  <h3>Reference</h3>
  <ul>
    <li>
      <a href="https://gist.github.com/pootsbook/ebe93033a1543c8ddf3ccde8ca5ea441">
        GitHub Gist
      </a> containing a full Ruby script of the above solution.
    </li>
  </ul>
</div>
