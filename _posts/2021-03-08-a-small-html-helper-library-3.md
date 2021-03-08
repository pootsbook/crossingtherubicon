---
layout: post
title: "A Small HTML Helper Library (3)"
---

Yesterday I [hit a mental block][hmb] in figuring out a way to make a nicer, more Ruby-ish DSL for generating HTML.

I have good news and bad news. The good news is that I worked it out, and I wasn’t as far off as I thought; the bad news is that I’m not overly happy with the internals. Let’s take a look.

As a reminder, I am going for something like this:

```
el :html, lang: "en-GB" do
  el :head do
    el :title do
      el :text do
        "Hello,"
      end
      el :text do
        World!
      end
    end
  end
end
```

My suggested approach was, in principle, sound, but the way I was attacking the solution lacked a little object-oriented finesse.

> Let’s start by defining a variable to hold the data structure that the `el` method will create

> It seems like it should be a simple case of tree walking

The first realisation based on the first quote above is that I indeed needed somewhere to store some sort of syntax tree. I was thinking a global variable that got modified, and maybe that could have worked but it wouldn’t have been very reusable.

Instead I should make each document an object backed by a class and store the state on an instance variable:

```
class HTMLDocument
  def initialize
    @doc = {}
  end
end
```

`@doc` would store the data structure described in the previous post, a keyed Hash for clarity, rather than a Ruby array functioning as a three-tuple.

```
{
  type: :html,
  attrs: { lang: 'en-GB' },
  children: [ … ]
}
```

It does have one implication for the syntax though, one I think I can live with. The document definition would resemble this:

```
HTMLDocument.new do |d|
  d.el :html, lang: "en-GB" do
    d.el :head do
      d.el :title do
        d.el :text do
          "Hello,"
        end
        d.el :text do
          "World!"
        end
      end
    end
  end
end.to_s
```

(Remember I’m splitting the text in the title into two nodes to cover the edge-cases inherent to block evaluation.)

## Tree Walking

After a few failed attempts at building the document’s abstract syntax tree (AST) into the `@doc` instance variable in one pass, I decided on another, two-step approach.

The difficulty I had was figuring out how to walk back up the tree any time I had reached the end of a branch and hit a leaf-node. My recursive solution was like the proverbial cat who only wanted to remain on one branch. In the absence of the fire service I decided to think inside the box: there was no cat. And no tree.

Instead of constructing the tree immediately I decided to introduce the notion of an `id` for a given node, stored in an instance variable `@node_id`, an auto-incrementing integer. Then I could store each node in a flat array instead of the Hash.

```
class HTMLDoc
  def initialize
    @doc = []
    @id = 0
  end
end
```

Let’s go for a walk. We first need to yield ourselves to the block that is given:

```
class HTMLDoc
  def initialize
    @doc = []
    @id = 0
    
    yield self if block_given?
  end
```

This is what allows us to kick off the document and expose our `el` method:

```
HTMLDocument.new do |d|
  d.el :html do
    …
  end
end
```

Our `el` method is then a variation on the `node` method now modified to work in this new block:

```
class HTMLDocument
  …
  def el(type, attributes={})
    @node_id += 1
    
    case type
    when :text
      @doc << { 
        id: @node_id,
        type: type,
        attrs: attributes,
        content: yield }
    else
      @doc << {
        id: @node_id,
        type: type,
        attrs: attributes
      }
      
      yield
    end
  end
end
```

Notice the peculiarity of `text` elements; there we yield the block into a content field to store it and essentially ‘stop’ walking. With other elements we keep walking by yielding outside of the element definition/capture.

What this yields us (sorry), is a structure like this in `@doc`:

```
[
  {:id=>1, :type=>:html, :attrs=>{:lang=>"en-GB"}}, 
  {:id=>2, :type=>:head, :attrs=>{}}, 
  {:id=>3, :type=>:title, :attrs=>{}}, 
  {:id=>4, :type=>:text, :attrs=>{}, :content=>"Hello,"},
  {:id=>5, :type=>:text, :attrs=>{}, :content=>"World!"}
]
```

But that only brings us half-way there. This is a flat structure, and ultimately we need to be able to turn this into a nested structure that matches the nesting of the Ruby blocks defining the document.

To do that we can introduce a `parent_id` variable which we can keep track of, similar to `node_id`.

```
class HTMLDocument
  def initialize
    @doc = []
    @node_id = 0
    @parent_id = 0
    
    yield self if block_given?
  end
end
```

To keep track of the parent when we walk into and out of leaves and branches we’ll need to ensure we wrap our recursive-ish `el` blocks with the relevant details. This was also a headbanger. In this case it’s a family tree and we need a `grandparent_id`. Stick man becomes unstuck man. 

We can add the `parent_id` to the element definition and keep track of who is the current parent at the document instance level and the current grandparent at the method/block level. This method,

```
def el(type, attributes = {})                          
  @node_id += 1                                        
  grandparent_id = @parent_id                          
        
  node = {
    id: @node_id, 
    parent_id: @parent_id, 
    type: type, 
    attrs: attributes
  }
  
  case type                                            
  when :text                                           
    @doc << node.merge(content: yield)
  else
    @doc << node
    @parent_id = @node_id
    
    yield
  end                                                  
    
  @parent_id = grandparent_id                          
end
```

(in which I have also tidied up the definition of a node to make the parent logic clearer) now gives us an array as below, which I have indented according to the `parent_id` to show how the parentage matches that of the document definition:

```
[
{:id=>1, :parent_id=>0, :type=>:html, :attrs=>{:lang=>"en-GB"}}, 
  {:id=>2, :parent_id=>1, :type=>:head, :attrs=>{}}, 
    {:id=>3, :parent_id=>2, :type=>:title, :attrs=>{}}, 
      {:id=>4, :parent_id=>3, :type=>:text, :attrs=>{}, :content=>"Hello,"}, 
      {:id=>5, :parent_id=>3, :type=>:text, :attrs=>{}, :content=>"World!"}
]
```

## Tree Construction

Now the final step is the transformation of this flat array into the recursive structure which we were aiming for at the beginning of this journey and _that_ will finally allow us to render the document to a string.

To do that I can define an `ast` function which essentially grabs the root element (the element with a `parent_id` of `0`) and then gathers the children of that element (elements with a `parent_id` equal to the current element `id`) and so on, all the way down to the end of each particular branch, producing the data structure which we can pass to our rendering method.

```
def ast
  root = @doc.find {|el| el[:parent_id] == 0 }
  { type: root[:type],
    attributes: root[:attrs],
    children: gather_children(root[:id]) }
end
    
def gather_children(node_id)
  @doc.select do |el|
    el[:parent_id] == node_id
  end.map do |el|
    if el[:type] == :text
      children = [el[:content]]
    else
      children = gather_children(el[:id])
    end

    { type: el[:type],
      attributes: el[:attrs],
      children: children }
  end
end
```

And, lo and behold, this is the AST we wanted:

```
{
  :type=>:html,
  :attributes=>{:lang=>"en-GB"},
  :children=>[
    {:type=>:head,
     :attributes=>{},
     :children=>[
       {:type=>:title,
        :attributes=>{},
        :children=>[
        {:type=>:text,
         :attributes=>{},
         :children=>["Hello,"]}, 
        {:type=>:text,
        :attributes=>{},
        :children=>["World!"]}]}]}]}
```

## Pulp Fiction

To render we can lift the methods from yesterday, make a couple of modifications for rendering children (since it’s no longer eager) and that should be us.

```
class HTMLDocument
  …
  def node(type, attributes = {}, children = [])
    case type
    when :text
      children.empty? ? "" : children.first
    when :input
      render_opening_tag(type, attributes)
    else
      [
        render_opening_tag(type, attributes),
        render_children(children),
        "</#{type}>"
      ].flatten.join("\n")
    end
  end
  
  def render_children(children)
    children.map do |child|
      node(child[:type], child[:attributes], child[:children])
    end.flatten.join("\n")
  end
  
  def render_opening_tag(name, attrs)
    if attrs.nil? || attrs.empty?
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
```

Let’s finish it off with the icing on the cake, the `to_s` method:

```
def to_s
  node(ast[:type], ast[:attributes], ast[:children])
end
```

And that’s that. I’m very happy to have worked it out.

There are a lot of improvements we could make to this code to clean it up even more. For example, memoizing the `ast` method, creating a `Node` class and passing those objects around instead of messing around with Hash keys the whole time (data clump refactoring), reducing the surface area of the data structures and removing the necessity to do so many transformations, cleanly separating out different concerns—the list could go on.

But I’m not going to do any of that. Why? Because I’m not very happy with how convoluted and verbose this is. It feels a little like architecture astronomy, using data structures and transformations just because we can. And don’t get me wrong, it was a fun exercise, but it’s not simple.

The nested block “grandparent” wrapping I stumbled upon makes me think we can generate the HTML string on the fly instead of some flattened tree or AST. It would greatly reduce the surface area of the code and get rid of the need for an underlying data abstraction completely. I’d like to try that next. Time to put the chainsaw away and do some hand crafting.

—*Monday 8th March 2021.*

[hmb]: https://www.crossingtheruby.com/2021/03/07/a-small-html-helper-library-2.html

<div class="references">
  <h3>Reference</h3>
  <ul>
    <li>
      <a href="https://gist.github.com/pootsbook/09702fb05cbac6dbc507169a3a56fed3">
        GitHub Gist
      </a> containing full Ruby script of the above solution.
    </li>
  </ul>
</div>

