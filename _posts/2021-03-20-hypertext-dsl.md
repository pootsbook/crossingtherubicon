---
layout: post
title: Hypertext DSL
---

In my explorations of Hypertext ([Introduction][ht], [Origins][hto], [Innards][hti]) and the subsequent [comparison][htc] with my own [HTML helper library][hhl] I deliberately limited myself to the core API. What I didn’t share, was that Mr. Martens also came up with a Markaby inspired DSL built on top of the core.

This is what a document written in the core API would look like:

```
Hypertext.new do |h|
  h.tag :html, lang: "en-GB" do
    h.tag :head do
      h.title do
        text "Hello, World!"
      end
    end
  end
end.to_s
```

And this is what the same document would look like using the DSL:

```
Hypertext::DSL.new do
  html lang: "en-GB" do
    head do
      title do
        text "Hello, World!"
      end
    end
  end
end.to_s
```

It’s beautiful.

It removes the necessity to pass `self` as an argument to the block `do |h|` when initializing and it removes the repetition of `h.tag` in favour of a method bearing the name of the HTML tag. It’s really clean; neat.

In contrast to Markaby, it doesn’t use any `method_missing` meta-programming ‘magic’ to introduce an element `class` and `id` API but favours keeping things simple, approachable and recognisable with a Hash for all attributes:

```
# Markaby
div.container.content! do
  …
end

# Hypertext::DSL
div class: "container", id: "content" do
  …
end
```

Hypertext retains the familiarity of normal Ruby syntax, and I feel is truer to the goal of an HTML DSL in Ruby. Markaby, with its API, tends towards libraries like [HAML][haml] and [Slim][slim] which are geared towards extreme concision.

As a result, Hypertext retains a much smaller API surface area and the implementation is incredibly short (55 SLOC). It does use `instance_eval` and `define_method`, so it’s not fair to say that it does no meta-programming, but these are very straightforward scenarios. In a future post I’ll walk through the API in more detail.

—*Saturday 20th March 2021.*

[ht]: https://www.crossingtheruby.com/2021/03/14/hypertext-write-html-in-ruby.html
[hto]: https://www.crossingtheruby.com/2021/03/15/hypertext-origins.html
[hti]: https://www.crossingtheruby.com/2021/03/16/hypertext-innards.html
[htc]: https://www.crossingtheruby.com/2021/03/17/hypertext-comparison.html
[hhl]: https://www.crossingtheruby.com/2021/03/09/a-small-html-helper-library-4.html
[haml]: https://haml.info
[slim]: http://slim-lang.com
