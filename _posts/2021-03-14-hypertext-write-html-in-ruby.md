---
layout: post
title: "Hypertext: Write HTML in Ruby"
---

I recently got an email from Michel Martens pointing me to a new Ruby library he had written. The timing was quite extraordinary. When his email arrived in my inbox I had just published my [initial attempt][sht] at a small HTML helper library in Ruby.

He may have been triggered by my post on integrating [Markaby with Syro][markaby] in which I compared Elm’s DSL with Markaby’s more Ruby-ish DSL. He wasn’t to know that I was about to embark on a journey to arrive at something which is almost identical in purpose and form to what he wrote.

After reading the README I shut down the browser. I thought it would be a good experiment to arrive at my own implementation without being influenced by his and then compare notes. Now that I’m done I’ve been studying Mr. Marten’s library in a bit more detail. I’ll compare the implementations in a future post.

The library is called [Hypertext][ht]. It is a library for authoring hypertext, writing HTML in Ruby.

This is what it looks like in action:

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

The resemblance is uncanny.

The library is very neat, with the core coming in at [84 SLOC][htc]. It is breathtakingly simple and at the same time fully-featured.

It offers the ability to construct an HTML document, allowing custom indentation and automatically escaping HTML reserved characters: `'`, `&`, `\`, `"`, `<`, and `>`.

It deals with void elements and text in addition to the standard opening and closing tags.

And that’s it. Just enough. No bells and whistles, nothing unnecessary, just what is needed and no more.

There is something so satisfying about artisanal software.

—*Sunday 14th March 2021.*

[sht]: https://www.crossingtheruby.com/2021/03/06/a-small-html-helper-library.html
[markaby]: https://www.crossingtheruby.com/2021/03/03/templating-with-markaby-in-syro.html
[ht]: https://github.com/soveran/hypertext
[htc]: https://github.com/soveran/hypertext/blob/e474acdaa3aeb0ec52a81098077fbe7ed7220942/lib/hypertext.rb
