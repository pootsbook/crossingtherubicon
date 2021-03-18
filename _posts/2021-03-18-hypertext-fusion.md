---
layout: post
title: Hypertext Fusion
---

After writing a [comparison][hvd] of Hypertext and my HTML Helper Library (HTMLDocument) my curiosity got the better of me. I wanted to see if I could add some of Hypertext’s elements to my makeshift library with the goal of feature parity.

Specifically, to add some of the missing features that would be critical for a production implementation, and also to more clearly define the trade-off between built-in and separated rendering.

Starting from my HTMLDocument ([code][htd]) as a base I’ll gradually modify it to fuse the code with Hypertext.

I’ll start by removing the void element definition, preferring the implicit presence or absence of a block to represent a closing or self-closing tag respectively.

```diff
-   VOID_ELEMENTS = %i[
-     area base br col embed hr img input 
-     link meta param source track wbr
-   ]
```

```diff
    def el(type, attrs={})
-     @doc << opening_tag(type, attrs)
- 
-     unless VOID_ELEMENTS.include? type
+     if block_given?
+       @doc << opening_tag(type, attrs)
        yield
        @doc << closing_tag(type)
+     else
+       @doc << self_closing_tag(type, attrs)
      end
    end
    
+   def self_closing_tag(name, attrs)
+     if attrs.empty?
+       "<#{name}>"
+     else
+       "<#{name} #{attributes(attrs)} />"
+     end
+   end
```

Now after reading Hypertext I can’t un-see the horrible branching in HTMLDocument which differentiates the cases for tags with and without attributes. With the addition of the `self_closing_tag` method we now have this duplicated as well. Let’s put that right.

```diff
    def opening_tag(name, attrs)
-     if attrs.empty?
-       "<#{name}>"
-     else
-       "<#{name} #{attributes(attrs)}>"
+     "<#{name}#{attributes(attrs)}>"
-     end
    end

    def self_closing_tag(name, attrs)
-     if attrs.empty?
-       "<#{name}>"
-     else
-       "<#{name} #{attributes(attrs)} />"
+     "<#{name}#{attributes(attrs)} />"
-     end
    end

    def attributes(attrs)
      attrs.map do |key, value|
-       %Q(#{key}="#{value}")
+       %[ #{key}="#{value}"]
-     end.join(" ")
+     end.join
    end
```

This looks significantly better and shorter. I also didn’t need the `Q` for the string definition and variable interpolation alongside quote literals so I’ve removed it, and switched to square brackets on a whim (`%Q()` => `%[]`). It does raise the question as to whether we need these separate methods or if, like Hypertext, we can simply in-line the tag output. Let’s in-line.

```diff
    def el(type, attrs={})
      if block_given?
-       @doc << opening_tag(type, attrs)
+       @doc << "<#{type}#{attributes(attrs)}>"
        yield
-       @doc << closing_tag(type)
+       @doc << "</#{type}>"
      else
-       @doc << self_closing_tag(type, attrs)
+       @doc << "<#{type}#{attributes(attrs)} />"
      end
    end
-  
-   def opening_tag(name, attrs)
-     "<#{name}#{attributes(attrs)}>"
-   end
-
-   def closing_tag(name)
-     "</#{name}>"
-   end
-
-   def self_closing_tag(name, attrs)
-     "<#{name}#{attributes(attrs)} />"
-   end
```

Ah, much better. And it’s doing wonders for my line count.

Another egregious omission from my library was handling value-less attributes. For such an attribute with a value of `false` it would cause a strange edge-case where the value would be interpolated as an empty string and thus technically, in HTML’s eyes, true (because the presence of a value-less attribute is equivalent to that attribute being true). Let’s put that right:

```diff
    def attributes(attrs)
      attrs.map do |key, value|
+       case value
+       when false
+       when true
+         %[ #{key}]
+       else
-       %[ #{key}="#{value}"]
+         %[ #{key}="#{value}"]
+       end
      end.join
    end
```

We also shouldn’t forget to escape the attribute value and also the output of the `text` method:

```diff
+   ENTITIES = {
+     "'" => '&#39;',
+     '&' => '&amp;',
+     '"' => '&quot;',
+     '<' => '&lt;',
+     '>' => '&gt;',
+   }
+
+   def escape(str)
+     str.gsub(/['&\"<>]/, ENTITIES)
+   end

    def attributes(attrs)
      …
-         %[ #{key}="#{value}"]  
+         %[ #{key}="#{escape(value.to_s)}"]
      …
    end

    def text(string)
-     @doc << string
+     @doc << escape(string)
    end 
```

I’ve opted to avoid a class method for now. I suspect, similar to the HTML formatting and indentation the reason Hypertext has chosen for `escape` as a class method, is to lift these formatting concerns into an area (the class) with a clear boundary (from the instance).

Let’s have a look at what we’ve accomplished before tackling the indentation:

```
class HTMLDocument
  ENTITIES = {
    "'" => '&#39;',
    '&' => '&amp;',
    '"' => '&quot;',
    '<' => '&lt;',
    '>' => '&gt;',
  }

  def initialize
    @doc = ""

    yield self if block_given?
  end

  def text(string)
    @doc << escape(string)
  end

  def el(type, attrs={})
    if block_given?
      @doc << "<#{type}#{attributes(attrs)}>"
      yield
      @doc << "</#{type}>"
    else
      @doc << "<#{type}#{attributes(attrs)} />"
    end
  end

  def to_s
    @doc
  end

  def attributes(attrs)
    attrs.map do |key, value|
      case value
      when false
      when true
        %[ #{key}]
      else
        %[ #{key}="#{escape(value.to_s)}"]
      end
    end.join
  end

  def escape(str)
    str.gsub(/['&\"<>]/, ENTITIES)
  end
end
```

All that in just forty-two (significant) lines of code. It boggles the mind.

Now we turn our attention to HTML formatting, namely indentation. Remember that Hypertext had a clean separation between ‘accumulation’ and formatting. It can do this because it stores a simple abstract syntax tree (AST) in the `@dom` instance variable, equivalent to `@doc` here.

I’d like to keep the eager generation of HTML rather than using an AST which rules out the clean separation. This way we can more clearly define the trade-off between AST with rendering separation, and no AST and no separation between document compilation and rendering.

I have a feeling that my foray into generating an AST in previous attempts will help me here. My initial thought is that, similar to the grandparent / parent assignment in the `el` method described in [this post][tp] and alongside `@indent` and `@level` instance variables that can be assigned on initialization, I can integrate the indentation quite straightforwardly.

```diff
-   def initialize
+   def initialize(indent = "  ", level = 0)
      @doc = ""
+     @indent = indent
+     @level = level
      
      yield self if block_given?
    end
```

The first step, ignoring our newly created instance variables is to put each piece of output on a new line:

```diff
    def text(string)
-     @doc << escape(string)
+     @doc << "#{escape(string)}\n"
    end
    
    def el(type, attrs={})
      if block_given?
-       @doc << "#{indent*level}<#{type}#{attributes(attrs)}>"
+       @doc << "#{indent*level}<#{type}#{attributes(attrs)}>\n"
        yield
-       @doc << "</#{type}>"
+       @doc << "</#{type}>\n"
      else
-       @doc << "<#{type}#{attributes(attrs)} />"
+       @doc << "<#{type}#{attributes(attrs)} />\n"
      end
    end
```

This gives us the following unindented HTML:

```
doc = HTMLDocument.new do |d|
  d.el :html, lang: "en-GB" do
    d.el :head do
      d.el :title do
        d.text "Hello, World!"
      end
    end
    d.el :body do
      d.el :input, { type: "text" }
    end
  end
end

puts doc.to_s

#=>
<html lang="en-GB">
<head>
<title>
Hello, World!
</title>
</head>
<body>
<input type="text" />
</body>
</html>
```

Let’s introduce the `@indent` and `@level` instance variables to the equation:

```diff
    def text(string)
-     @doc << "#{escape(string)}\n"
+     @doc << "#{@indent * @level}#{escape(string)}\n"
    end
  
    def el(type, attrs={})
+     indentation = @indent * @level
      if block_given?
-       @doc << "<#{type}#{attributes(attrs)}>\n"
+       @doc << "#{indentation}<#{type}#{attributes(attrs)}>\n"
+       @level += 1
        yield
+       @level -= 1
-       @doc << "</#{type}>\n"
+       @doc << "#{indentation}</#{type}>\n"
      else
-       @doc << "<#{type}#{attributes(attrs)} />\n"
+       @doc << "#{indentation}<#{type}#{attributes(attrs)} />\n"
      end
    end
```

And that should be it. Using the same document definition as above, and running it with the updated code, we now have the following output: 

```
<html lang="en-GB">
  <head>
    <title>
      Hello, World!
    </title>
  </head>
  <body>
    <input type="text" />
  </body>
</html>
```

Done! 

To start the indentation at a deeper level, or to have say, four spaces for indentation (who would entertain even the thought of such a monstrosity?!) we can pass in the indent and level on initialization:

```
doc = HTMLDocument.new("    ", 1) do |d|
  …
```

And the same document definition now yields the following output:

```
    <html lang="en-GB">
        <head>
            <title>
                Hello, World!
            </title>
        </head>
        <body>
            <input type="text" />
        </body>
    </html>
```

The full code can be seen in this [gist][final] and comes in at under 50 SLOC. I like the concision of this approach although the formatting concerns do indeed muddy the code and obfuscate the intent. Which do you prefer? The separation of formatting concerns in Hypertext backed by an AST or the eager generation of HTML including formatting in HTMLDocument? I’m interested to hear your thoughts.

—*Thursday 18th March 2021.*

[hvd]: https://www.crossingtheruby.com/2021/03/17/hypertext-comparison.html
[htd]: https://gist.github.com/pootsbook/c3869f089af6fb1cf1fd53bc0250961b
[tp]: https://www.crossingtheruby.com/2021/03/08/a-small-html-helper-library-3.html
[final]: https://gist.github.com/pootsbook/65819c79722d93c9baf53d984f1884b2
