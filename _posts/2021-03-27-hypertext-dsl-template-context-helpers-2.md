---
layout: post
title: "Hypertext DSL: Template Context (Helpers) (2)"
---

In a previous post I attempted to introduce [helper methods][hhm] into the Hypertext DSL and came up short. I had been trying to introduce the methods into the `Hypertext::DSL` context and failing.

Having had the opportunity to sleep on it and some more time to think about it, it struck me that that Hypertext DSL is tiny. So instead of mixing a context into the Hypertext DSL, what if we mix the Hypertext DSL into the context.

Rather than use the `Hypertext::DSL` class, we can simply lift the code into a module, `dsl.rb`:

```
require "hypertext"

module DSL
  TAGS = [:a, :abbr, :address, :area, :article, :aside,
  :audio, :b, :base, :bdi, :bdo, :blockquote, :body, :br,
  :button, :canvas, :caption, :cite, :code, :col, :colgroup,
  :data, :datalist, :dd, :del, :details, :dfn, :dialog, :div,
  :dl, :dt, :em, :embed, :fieldset, :figcaption, :figure,
  :footer, :form, :h1, :h2, :h3, :h4, :h5, :h6, :head,
  :header, :hgroup, :hr, :html, :i, :iframe, :img, :input,
  :ins, :kbd, :label, :legend, :li, :link, :main, :map,
  :mark, :meta, :meter, :nav, :noscript, :object, :ol,
  :optgroup, :option, :output, :p, :param, :picture, :pre,
  :progress, :q, :rb, :rp, :rt, :rtc, :ruby, :s, :samp,
  :script, :section, :select, :slot, :small, :source, :span,
  :strong, :style, :sub, :summary, :sup, :table, :tbody, :td,
  :template, :textarea, :tfoot, :th, :thead, :time, :title,
  :tr, :track, :u, :ul, :var, :video, :wbr]

  def initialize(&block)
    @ht = Hypertext.new
    instance_eval(&block)
  end

  TAGS.each do |tag_name|
    define_method(tag_name) do |attributes = {}, &block|
      @ht.tag(tag_name, attributes, &block)
    end
  end

  def append(content)
    @ht.append(content)
  end

  def text(content)
    @ht.text(content)
  end

  def to_a
    @ht.to_a
  end

  def to_s
    @ht.to_s
  end
end
```

The only thing I changed was `class` to `module`, the rest is precisely as it stands in the Hypertext library.

Now we can create a context class that mixes in the DSL:

```
require_relative "./dsl"

class Context
  include DSL

  def id_generator
    "id-#{rand(100)}"
  end
end
```

We’ve defined an `id_generator` method as a proof of concept that can be called from the template, `index.ht`:

```
html lang: "en-GB" do
  head do
    title do
      text "Hello, World!"
    end
  end
  body do
    h1 id: id_generator do
      text "Welcome"
    end
  end
end
```

This time, if we put everything together by creating an instance of the context class (including our mixed in DSL) and pass the template to it:

```
require_relative "./context"

def render(template)
  instance_eval <<-CODE
    Context.new do
      #{File.read(template)}
    end.to_s
  CODE
end

puts render("index.ht")
```

Then we hit the jackpot:

```
<html lang="en-GB">
  <head>
    <title>
      Hello, World!
    </title>
  </head>
  <body>
    <h1 id="id-62">
      Welcome
    </h1>
  </body>
</html>
```

Notice the `id-62` value for the `id` attribute on the `h1`. This has been generated at random from our context method.

So now we have a proof of concept for both [variable interpolation][hvi] and helper methods. I’m not sure if the paths I have trodden are the best paths that could be taken, but it’s a start at least. The DSL can be extended to include variables and to allow a higher level DSL via helper methods.

—*Saturday 27th March 2021.*

[hhm]: https://www.crossingtheruby.com/2021/03/26/hypertext-dsl-template-context-helpers.html
[hvi]: https://www.crossingtheruby.com/2021/03/24/hypertext-dsl-template-context-variables.html
