---
layout: post
title: "A Small HTML Helper Library (5)"
---

I was so overjoyed with [my discovery yesterday][shl4] that I forgot to polish up the final artefact.

There are a couple of improvements I think that we can make to the API and logic, namely how we handle text, and a more extensive switching logic for [HTML void elements][hve] (elements that do not need to be closed).

At the moment, because I was trying to find a unified API for both HTML elements and text nodes I treated a text node just like an HTML element:

```
d.el :text do
  "Content"
end
```

I don’t like this for two reasons. Well, three actually. It isn’t faithful to the underlying structure we are trying to abstract, a text node has no attributes, and is not technically an element. As a result of carrying this unnecessary weight it’s overly verbose in the template. And lastly, it clouds our rendering logic a little by adding an extra branch to the decision tree.

What if we introduced a new `text` method to handle text nodes. It wouldn’t need to take a block, so we can just pass a string. In the ‘template’:

```
d.el :h1 do
  text "Hello, World!"
end
```

And in the `HTMLDocument` class:

```
def text(string)
  @doc << string
end
```

This then transforms our `el` method by removing the `:text` case from the case statement:

```
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
```

To this:

```
def el(type, attrs={})
  case type
  when :input
    @doc << opening_tag(type, attrs)
  else
    @doc << opening_tag(type, attrs)
    yield
    @doc << closing_tag(type)
  end
end
```

As you can see, as a remnant from our initial use-case, we are only catering for the `input` void element. There are a [number][hve] of different void elements and we would do well to cater for them all. Let’s introduce them here in a constant:

```
VOID_ELEMENTS = %i[
  area base br col embed hr img input 
  link meta param source track wbr
]

def el(type, attrs={})
  case type
  when *VOID_ELEMENTS
    @doc << opening_tag(type, attrs)
  else
    @doc << opening_tag(type, attrs)
    yield
    @doc << closing_tag(type)
  end
end
```

That should now handle those other cases of void elements.

Now, while staring at a case statement with just two cases is bad enough, when one case duplicates one of the statements in the other then you know it’s time for a change. Let’s clean that up:

```
def el(type, attrs={})
  @doc << opening_tag(type, attrs)
  
  unless VOID_ELEMENTS.include? type
    yield
    @doc << closing_tag(type)
  end
end
```

Ah. Much better. Although I’ve broken the golden rule of using an `unless` across multiple lines. We should have an `Enumerable#exclude?` method as unpalatable as it sounds. I was caught between a rock and a hard place. The alternative is to use,

```
if !VOID_ELEMENTS.include? type
```

and my keen eye for aesthetic sensibility thinks it worse to negate at the beginning of a constant given the resulting obscurity. 

To show you the levels to which my dissatisfaction will drive me I did come up with:

```
if Set[*VOID_ELEMENTS].disjoint? Set[type]
```

but it really is an acquired taste.

Anyway, that digression aside, we have cleaned up our code significantly. Here’s the polished class:

```
class HTMLDocument
  VOID_ELEMENTS = %i[
    area base br col embed hr img input link meta param source track wbr
  ]

  def initialize
    @doc = ""

    yield self if block_given?
  end

  def text(string)
    @doc << string
  end

  def el(type, attrs={})
    @doc << opening_tag(type, attrs)

    unless VOID_ELEMENTS.include? type
      yield
      @doc << closing_tag(type)
    end
  end

  def to_s
    @doc
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
end
```

```
doc = HTMLDocument.new do |d|
  d.el :html, lang: "en-GB" do
    d.el :head do
      d.el :title do
        d.text "Hello, "
        d.text "World!"
      end
    end
    d.el :body do
      d.el :input, { type: "text" }
    end
  end
end.to_s
```

And I’m sure there are lots more tricks in the bag. What if we want to format our HTML? Indent it? Can we embed one document inside another document? I don’t know. But let’s find out.

—*Wednesday 10th March 2021.*

[shl4]: https://www.crossingtheruby.com/2021/03/09/a-small-html-helper-library-4.html
[hve]: https://html.spec.whatwg.org/multipage/syntax.html#elements-2
