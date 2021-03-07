---
layout: post
title: "A Small HTML Helper Library (2)"
---

Previously, I put together a proof of concept function for [generating HTML in pure Ruby][ghpr]. It was based on nesting a single function with a signature common to nodes in HTML. The code was one main `node` function with two small helper functions and this is what the HTML in Ruby looked like:

```
node(:html, { lang: "en-GB" }, [
  node(:head, {}, [
    node(:title, {}, [
      node(:text, {}, ["Hello, World!"])
    ])
  ])
])
```

Now most Ruby developers will recoil at the syntax, and so they should. Look at all those horrible square brackets, parentheses and curly braces. The absence of these syntactical aids is precisely why people flock to Ruby. This is the perfect opportunity to employ Ruby blocks. That is, children should be specified in a nested block and not with an array. It could, and should, therefore look something more like this:

```
node :html, lang: "en-GB" do
  node :head do
    node :title do
      node :text do
        "Hello, World!"
      end
    end
  end
end
```

After taking a moment to regain your breath as you admire the elegance let’s consider how we might bring about such beauty. As I see it, there’s one challenge to overcome and that has to do with the difference between an array of children and block containing children as individual method statements:

```
# array syntax
node(:title, {}, [
  node(:text, {}, ["Hello "]),
  node(:text, {}, ["World!"])
])
#=> <title>Hello World!</title>

# block syntax
node :title do
  node :text { "Hello " }
  node :text { "World!" }
end
#=> <title>World!</title>
```

In an array, the return value is each element in the array. But in a block, the return value is simply the return value of the last statement in the block:

```
def optional_block
  puts yield if block_given?
end

optional_block do
  "Hello" # statement 1
  "World" # statement 2
end
```

I think the way around this is to use the methods in the HTML definition to build a data structure representing the HTML and then render it in one pass, versus what happens now with rendering happening eagerly as you call each method.

Thinking of our education in functional programming, and the interchangeability of functions and data, then we can trust a data structure that mirrors our method structure:

```
[:html, { lang: "en-GB" }, [
  [:head, {}, [
    [:title, {}, [
      [:text, {}, ["Hello, World!"]]
    ]]
  ]]
]]
```

A possibility could also be a keyed hash, I’m not yet quite sure what is best:

```
{ type: :html, attrs: { lang: "en-GB" }, children: [ … ] }
```

But to render, we simply need to write a recursive function to walk the tree (whichever format we choose) and produce the HTML. Let’s give it a go.

Let’s keep our `node` method around from last time to re-use and call our new block taking method `el`. The example Ruby HTML only has one modification (apart from the syntax) which is to separate the string into two text nodes to prove that we cover the case of sibling blocks mentioned above.

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

Let’s start by defining a variable to hold the data structure that the `el` method will create, and we’ll use the keyed Hash syntax rather than the array for clarity in demonstration. That way we refer to each argument by key (`type`, `attrs`, `children`) rather than by array index.

---

And this is where I hit a wall. It seems like it should be a simple case of tree walking and that escapes me right now. I think it’s time to sleep on it.

Any ideas? Let me know. Is the approach sound? It feels like I need to go to the ends of each branch and back again. Maybe this isn’t possible in the current form. 

One workaround might be to scrap this intermediate representation to output to two (global for now) strings “opening” (append) and “closing” (prepend) and when we’re done in the middle we join them together and hey presto our HTML.

—*Sunday 7th March 2021.*

[ghpr]: https://www.crossingtheruby.com/2021/03/06/a-small-html-helper-library.html
