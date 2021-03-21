---
layout: post
title: Hypertext DSL Implementation
---

In a previous post, I introduced [Hypertext’s DSL][hdsl] which is a thin, experimental layer around the core Hypertext API designed to reduce the boilerplate for defining a hypertext document (or fragment) in Ruby.

It dynamically defines a method for each HTML5 tag which acts as a wrapper for the corresponding `Hypertext#tag` method and arguments. For example, for the `head` tag it replaces this:

```
# Hypertext Core

h.tag :html, lang: "en-GB" do
  …
end
```

with this:

```
# Hypertext DSL

html lang: "en-GB" do
  …
end
```

There are two key things going on under the hood. The first is the method definition mentioned above, which looks like this:

```
TAGS.each do |tag_name|
  define_method(tag_name) do |attributes = {}, &block|
    @ht.tag(tag_name, attributes, &block)
  end
end
```

The `TAGS` constant referenced above is simply an array of symbols representing the set of HTML5 tags included in the DSL:

```
TAGS = [:a, :abbr, :address, :area, :article, … ]
```

You can see that the resulting methods are simply delegating to the `Hypertext#tag` method, passing in the tag name (which is then the method name in the DSL) as the first argument, and passing in the attributes and block as the second and third arguments. If you squint it looks an awful lot like method currying, but then with a method name later becoming an argument.

The second important aspect has to do with the `@ht` instance variable referenced in these method definitions. This is an instance of the `Hypertext` class, exposing the core API to the DSL. This object is instantiated on DSL initialization and assigned to `@ht` and then the block passed to the DSL is executed in the context of the receiver via `instance_eval`, thus allowing all methods to access the instance variable `@ht` and exposing the HTML tag methods without the need for passing `self` and calling the methods on that argument to the block.

```
Hypertext::DSL.new do
  html lang: "en-GB" do
    …
  end
end.to_s
``` 

is calling this code:

```
def initialize(&block)
  @ht = Hypertext.new
  instance_eval(&block)
end
```

The only other two methods in the DSL class are `text` and `to_s` which are both also transparently delegating to the corresponding core API methods:

```
def text(content)
  @ht.text(content)
end

def to_s
  @ht.to_s
end
```

Believe it or not, aside from the long list of HTML tags which I truncated for readability, we’ve now seen all of the code from the DSL in this blog post. You can checkout the full 55 SLOC on [Hypertext’s GitHub repository][dslc].

For me, this is the benefit of the trade-off I mentioned in the [previous post][hdsl]. By choosing for a pure Ruby DSL, the implementation is radically simplified. 

What is also a triumph of good design, in my eyes at least, is that the core can be used completely independently of the DSL. It stands on its own. The DSL is a _layer_ on top of the core.

I don’t know if this was intentional from the start, but it proves to me that having solid primitives allows for judicious extension. That building the foundation thoughtfully and carefully enables higher-level design that doesn’t have to resort to building anew.

That’s something worth striving for.

—*Sunday 21st March 2021.*

[hdsl]: https://www.crossingtheruby.com/2021/03/20/hypertext-dsl.html
[dslc]: https://github.com/soveran/hypertext/blob/e474acdaa3aeb0ec52a81098077fbe7ed7220942/lib/hypertext/dsl.rb
