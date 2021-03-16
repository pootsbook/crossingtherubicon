---
layout: post
title: Hypertext Innards
---

In previous posts I [introduced Hypertext][iht] and shed a little light into [Hypertext’s origins][hto]. As promised, I would like to take a look at how Hypertext has been implemented.

As a refresher, the API looks like this:

```
html = Hypertext.new do |h|
  h.tag :html do
    h.tag :head do
      h.tag :title do
        h.text "Hello, World!"
      end
    end
    h.tag :body do
      h.tag :h1 do
        h.text "Welcome"
      end
    end
  end
end

puts html.to_s

# <html>
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <h1>
#       Welcome
#     </h1>
#   </body>
# </html>
```

Everything revolves around the `Hypertext` class, which exposes, on the surface, a `tag` instance method for creating HTML tags and a `to_s` instance method for producing the final HTML output.

The `initialize` method (`new`) is used for instantiating a `Hypertext` object and it takes a block yielding itself as the block’s argument, which allows the calling of the `tag` method on that instance.

`initialize` also initializes an instance variable `@dom` which is an array, used to store an abstract syntax tree of the DOM built up by the `tag` Ruby HTML DSL.

```
class Hypertext
  …
  def initialize
    @dom = []

    if block_given?
      yield self
    end
  end
  …
end
```

The `tag` method is then tasked with gathering the HTML element with the first parameter and any HTML attributes in an optional second parameter:

```
  def tag(name, attributes = {})
    …
  end
```

The first task it carries out is to _compile_ the attributes:

```
  def tag(name, attributes = {})
    atts = compile(attributes)
    …
  end
  
  def compile(attributes)
    attributes.map do |key, val|
      case val
      when false
      when true
        %[ #{key}]
      else
        %[ #{key}="#{escape(val.to_s)}"]
      end
    end.join
  end
```

This maps over the attributes that have been passed in (remember a map over an empty attributes hash will return `nil` which will later evaluate to an empty string, `""`). It uses a case statement which handles three cases. If an attribute is set to `false`, then it returns `nil`; if an attribute is set to `true` then it returns the attribute key as a string; and lastly for a key whose value is neither true nor false, it outputs the key-attribute pair in the format expected in HTML, `key="value"`. Then it joins those attribute strings together with `String.join`.

There are three things I like about this, the first of which I hadn’t thought about. For values of `true` and `false` it is usual in HTML to only print the attribute name without a value, e.g. `<input type="text" disabled>`, and Hypertext handles this nicely.

Secondly, it automatically prefixes the individual attribute string with a space, so there is no need later for piecing together the tag name with the attributes while fiddling with spaces. It solves the problem of “n+1” spaces when using join. It’s a nice touch.

Thirdly, I like how it uses the `%[]` syntax for declaring a string because it allows the use of quotes inside it as well as interpolation.

One interesting thing to note is that the value of the attribute is being escaped `escape(val.to_s)`.

The escape instance method looks like this:

```
  def escape(str)
    self.class.escape(str)
  end
```

which delegates to `Hypertext::escape` (class method). This is what the class method `escape` looks like:

```
  def self.escape(str)
    str.gsub(/['&\"<>]/, ENTITIES)
  end  
```

And it’s pulling string substitutions from a mapping in the `Hypertext::ENTITIES` constant:

```
  ENTITIES = {
    "'" => '&#39;',
    '&' => '&amp;',
    '"' => '&quot;',
    '<' => '&lt;',
    '>' => '&gt;',
  }
```

It’s looking for `'`, `&`, `\`, `"`, `<`, and `>` characters, which as far as I am aware are known in XML as _predefined entities_ which also carry over into HTML. That is, they have special meaning for the parser and thus, for a document to be valid HTML, the values in both attribute values and text nodes must be escaped, and the corresponding HTML character entities are used in their place.

I learned that you can pass a hash to `gsub` which allows you to substitute a range of values at once according to the mapping in the provided hash.

Let’s turn our attention back to the `tag` method, having compiled our attributes into the local `atts` variable.

```
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
```

It then checks if a block has been passed to the `tag` method. If we deal with the second branch first then a method call without a block means that we are dealing with a void HTML element, one that has no children or closing tag. In this case the HTML string for that self-closing tag is added to the `@dom` instance variable.

If the `tag` method does have a block, then we know that the block for that tag contains the children for that tag. This is where it is a little complicated to grok on first reading. What is straightforward is that the branch is prefixed and suffixed with the given opening and closing HTML for that tag:

```
@dom << "<#{name}#{atts}>"
…
@dom << "</#{name}>"
```

Both of which are added to the `@dom` instance variable in similar fashion to the void element. These opening and closing tag statements enclose the nested block, and in order to properly capture the block’s contents, along with any eventual (nested) descendants, there’s a bit of housekeeping going on that needs a bit of explanation:

```
original, @dom = @dom, []
yield
@dom = original << @dom
```

I think an example here will be most illustrative. For the following truncated Hypertext definition:

```
html = Hypertext.new do |h|
  h.tag :head do
    h.tag :title do
    end
  end
end
```

The `head` tag is the outer element containing a block which defines a `title` tag. If we were to execute this code then we would first handle the `head` tag:

```
@dom << "<head>"

#=> @dom = [
  "<head>"
]
```

what then happens looks a bit like jiggery-pokery, but I can assure you it is all above board. We’re doing double assignment:

```
# from method definition
original, @dom = @dom, []

# in our example
original = [
  "<head>"
]
@dom = []
```

So when we yield to the block in the next line, the instance variable for the object is an empty array, and the `tag` method defines the `original` variable which is now storing the DOM AST up until that point.

Yielding calls the tag method on the instance, with `@dom` now set to an empty array `[]`, as if beginning all over again, and it in turn, populates the empty `@dom` array with the `title` opening and closing tag `["<title>", "</title>"]`.

After yielding the outer call to `tag` (for `head`) then gathers up the “original” AST and combines it with the AST that was produced in the yielding to the block:


```
@dom = original << @dom

#=> @dom = [
  "<head>", 
  [
    "<title>",
    "</title>"
  ]
]
```

And then it adds the closing tag:

```
@dom << "</#{name}>"

#=> @dom = [
  "<head>", 
  [
    "<title>",
    "</title>"
  ],
  "</head>"
]
```

So that’s how it builds up its internal AST. The only additional method for the DSL is the `text` method, which takes a string, and escapes it with the same method that escaped the attribute values:

```
  def text(value)
    @dom << escape(value)
  end
```

This string is simply added to the `@dom` AST. So:

```
html = Hypertext.new do |h|
  h.tag :head do
    h.tag :title do
      h.text "Hello, World!"
    end
  end
end

#=> @dom = [
  "<head>", 
  [
    "<title>", 
    [
      "Hello, World!"
    ],
    "</title>"
  ],
  "</head>"
]
```

Now that the AST has been built up, we can use the `to_s` to render the AST to HTML.

```
  def to_s(indent = "  ")
    self.class.render(@dom, indent)
  end
```

`to_s` takes an optional parameter, `indent` which defaults to two spaces `"  "`. It calls the class method `Hypertext::render` and passes the `@dom` AST and the indent. 

```
  def self.render(array, indent = "  ", level = 0)
    array.map do |element|
      if Array === element
        render(element, indent, level + 1)
      else
        sprintf "%s%s\n", indent * level, element
      end
    end.join
  end
```

`render` itself has an optional `level` parameter defaulting to `0` which we will see in use presently.

It takes the `@dom` array and maps over each (array) element. If the element is itself of type `Array` then it recursively calls render while increasing the indent level by 1.

If the element is not an `Array` then it prints the element, prefixed by the `indent` (two spaces) multiplied by the `level`. The element will be one of: an opening tag, a closing tag, a self-closing tag (void element) or a string (text node). In Ruby terms these are all just strings without any particular encoded semantic value. It suffixes the resulting string with a newline character and then joins the whole array together into one string which it returns.

The indenting machinery with the recursive auto-incrementing level is a really elegant and almost invisible way for handling what is in effect a non-core, presentational concern. That the rendering is separated out into a class method which making this boundary explicit feels very refined.

For completeness, both `compile` and `escape` are private methods and there is also a `to_a` method which simply returns the `@dom` instance variable, likely useful for testing and debugging:

```
  def to_a
    @dom
  end
```

And that’s it. 84 lines of code, a tight API, and a (micro) library with a few tricks up its sleeve. Enduringly simple. And a good foundation for future extension.

My compliments Mr. Martens, you’ve done it again.

—*Tuesday 16th March 2021.*

[iht]: https://www.crossingtheruby.com/2021/03/14/hypertext-write-html-in-ruby.html
[hto]: https://www.crossingtheruby.com/2021/03/15/hypertext-origins.html
