---
layout: post
title: Hypertext Origins
---

In a [previous post][pp], describing the [Hypertext][ht] library I wrote:

> He [Mr. Martens] may have been triggered by my post on integrating Markaby with Syro in which I compared Elm’s DSL with Markaby’s more Ruby-ish DSL.

The truth is always stranger than fiction. We had been talking on the phone just over three weeks ago about a declarative form builder. After the call Mr. Martens had been playing around with different ideas as an intermediate step towards the form builder. A few days later he had something which resembled the final Hypertext library, but it wasn’t until March 6th that he decided to cut the gem and release it to the world:

> I started working on Hypertext on or around February 23rd, after our call and upon receiving your email with the dream form builder. I did some experiments and on February 28th arrived at something very close
to the final version. It was just a class called Dom. It was on March 6th, after reading your post, that I decided to wrap it as a gem and change its name to Hypertext.

What is even more interesting is that he shared some of the development of the code before he arrived at the final version. It’s always encouraging to recognise that these polished APIs don’t just fall out of the sky. And indeed, could even be termed another case of [expansion and contraction][eac].

That is, there was first an intermediate representation of the DOM that looked like this:

```
html = [
  "<form>",
  [
    "<input />",
    "<input />",
    "<input />",
    "<div>",
    [
      "<p>",
      [
        "hello world",
      ],
      "</p>",
    ],
    "</div>"
  ],
  "</form>",
]

puts tree(html, "  ")
```

where `tree` is a method that outputs the final HTML given the representation as the first parameter, and does so according to the spacing passed in as the second parameter.

> That `tree` function later became `Dom.tree`, and it is now `Hypertext.render`. Then I wrote code for building that sort of AST.

I want to examine the internals of Hypertext in a future post, and then another post comparing Hypertext to my own final solution.

For now, it’s fascinating to see a similar journey in Hypertext’s genesis. It’s also interesting to see the independent development of code based on the same idea, as well as the cross-pollination of ideas that arose from talking and writing about the code and the process:

> So indeed it was inspired by you—not by the post, but by our conversation; and not as a goal, but as an intermediate step

I’m excited to see where this intermediate step leads.

—*Monday 15th March 2021.*

[pp]: https://www.crossingtheruby.com/2021/03/14/hypertext-write-html-in-ruby.html
[ht]: https://github.com/soveran/hypertext
[tp]: https://www.crossingtheruby.com/2021/03/06/a-small-html-helper-library.html
[eac]: https://www.crossingtheruby.com/2021/03/12/expansion-and-contraction.html
