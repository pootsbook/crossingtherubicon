---
layout: post
title: A Small HTML helper library
---

In a previous post I wrote a [form helper with Ruby][fhr] and I’d like to take the basics a little further. Perhaps we’ll arrive at something like Markaby.

This is the basic method I wrote that acted as a means to building an HTML tag:

```
def render_tag(tag_name, attributes = {}, content = nil)
  html = "<#{tag_name}"
  attributes.each do |key, value|
    html << %Q(#{key}="#{value} ")
  end
  if !content.nil?
    html << ">#{content}</#{tag_name>"
  else
    html << ">"
  end
end
```

It falls short in a number of different ways. Firstly, we should probably speak about HTML _elements_ or _nodes_ which are represented by _tags_. 

Elements have a set of _attributes_. An attribute set for a given element is made up of global attributes applying to all HTML elements, and potentially also attributes specific to that element or a subset of elements to which that element belongs. An example of a global element attribute is `id` or `class`. An example of an attribute specific to the `a` element is `href`, an `img` element has the `src` attribute.

Secondly, while we supply the possibility for a tag to have content, this is quite limited. It would be more proper to speak of an node having _children_. An HTML document is essentially a tree of nodes.

If we borrow the structure from Elm, we can arrive at an API for our `render_tag` method as follows, while renaming it to `node`:

```
def node(tag, attributes = {}, children = [])
end
```

This gives us the ability to recursively nest our functions to arrive at the nested HTML structure:

```
node(:html, { lang: "en-GB" }, [
  node(:head, {}, [
    node(:title, {}, [
      text("Hello, World!")
    ]
  ])
])
```

Note the `text` method which I have added to represent a text node. A text mode is almost always the outermost (innermost?) leaf or node of the tree:

```
def text(string)
  string
end
```

We can probably do away with the ceremony and leave it out, opting instead to just provide the string:

```
node(:title, {}, ["Hello, World!"])
```

If we wanted to retain the same API for all nodes then we could define a text node like this:

```
node(:text, {}, ["Hello, World!"])
```

Let’s do that now for reasons that will be come clear in a moment.

We’ll turn our attention to the internals of the `node` function to see how we might arrive at an implementation. The first thing to notice is that this is recursive. This always means we need a base case, that is, when we stop calling the children, and in our case, when we get to the leaf-node of the tree, a text node:

```
def node(type, attributes = {}, children = [])
  case type
  when :text
    children.empty? ? "" : children.first
  else
    …
  end
end
```

When we get to the other cases we have for ourselves, `label` and `input` also represent two different categories of node. A label behaves as normal with an opening tag and a closing tag, while an input has no closing tag, and indeed no text node, meaning it also behaves as an outer leaf-node:

```
def node(type, attributes = {}, children = [])
  case type
  when :text
    children.empty? ? "" : children.first
  when :input
    "<#{type} #{stringify_attrs(attributes)}>"
  else
    …
  end
end

def stringify_attrs(attrs)
  return "" if attrs.empty?
  
  attrs.map do |key, value|
    %Q(#{key}="#{value}")
  end.join(" ")
end
```

Notice the helper method that I’ve introduced to stringify the attributes from a Ruby Hash to something resembling the HTML attribute strings. 

The last step is to take a conventional HTML opening and closing tag like `label` and define this case. We could add another case statement with the label directly, but we’ll just use the else clause for now. I’m not sure if we come across any other categories of tag that avoid these three rules.

```
def node(type, attributes = {}, children = [])
  case type
  …
  else
    [
      "<#{type} #{stringify_attrs(attributes)}>", 
      children, 
      "</#{type}>"
    ].flatten.join("\n")
  end
end

def stringify_attrs(attrs)
  …
end
```

Putting everything together it then looks like this:

```
def node(type, attributes = {}, children = [])
  case type
  when :text
    children.empty? ? "" : children.first
  when :input
    "<#{type} #{stringify_attrs(attributes)}>"
  else
    [
      "<#{type} #{stringify_attrs(attributes)}>",
      children,
      "</#{type}>"
    ].flatten.join("\n")
  end
end

def stringify_attrs(attrs)
  return "" if attrs.empty?

  attrs.map do |key, value|
    %Q(#{key}="#{value}")
  end.join(" ")
end
```

Let’s extract a `render_opening_tag` method to avoid duplication, get rid of the extra space that gets generated (e.g. `<head >`), and eliminate the need for a sentinel or guard clause in `stringify_attrs` in the case when a tag has no attributes.

```
def render_opening_tag(name, attrs)
  if attrs.empty?
    "<#{name}>"
  else
    "<#{name} #{stringify_attrs(attrs)}>"
  end
end
```

Putting that all together in a script with the example from the start:

```
def node(type, attributes = {}, children = [])
  case type
  when :text
    children.empty? ? "" : children.first
  when :input
    render_opening_tag(type, attributes)
  else
    [
      render_opening_tag(type, attributes), 
      children, 
      "</#{type}>"
    ].flatten.join("\n")
  end
end

def render_opening_tag(name, attrs)
  if attrs.empty?
    "<#{name}>"
  else
    "<#{name} #{stringify_attrs(attrs)}>"
  end
end

def stringify_attrs(attrs)
  attrs.map do |key, value|
    %Q(#{key}="#{value}")
  end.join(" ")
end

doc = node(:html, { lang: "en-GB" }, [
  node(:head, {}, [
    node(:title, {}, [
      node(:text, {}, ["Hello, World!"])
    ])
  ])
])

puts doc
```

And then running the script yields:

```
<html lang="en-GB">
<head>
<title>
Hello, World!
</title>
</head>
</html>
```

That was fun!

—*Saturday 6th March 2021.*

[fhr]: https://www.crossingtheruby.com/2021/03/05/forms-in-syro-with-scrivener-2.html 
