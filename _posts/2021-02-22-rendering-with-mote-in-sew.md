---
layout: post
title: Rendering with Mote in Sew
---

Previously I looked at how simply [Mote][mote] goes about its work as a templating engine and how it inspired me to write my own minimal static site generator, [Sew][sew]. I’d like to briefly look at how I then use Mote to render the pages that have been collected by Sew’s reading of the file system.

The basic data relating to a page is collected in the `site.pages` array and looks like this:

```
{
  id: "index",
  locale: :en,
  title: "About",
  body: "<h1>About</h1>\n<p>Hello</p>"
}
```

Rendering then involves presenting the available site and page data to Mote in a context object and rendering a template that is able to call methods and access variables provided in the context.

I can create a simple `Context` class with access to the page data:

```
class Context
  attr_accessor :page
  
  def initialize(page)
    @page = page  
  end
end
```

This can then be passed to Mote as the context for rendering:

```
def render(context)
  Mote.parse(context.page.body, context, context.page.keys)[context.page]
end
```

What we can also do is include this render function in the context itself to simplify it a little:

```
class Context  
  attr_accessor :page
  
  def initialize(page)
    @page = page  
  end
  
  def render
    Mote.parse(page.body, self, page.keys)[page]
  end
end
```

This works in the simple case for a single page, but what if we want to add something like a layout? Let’s define a layout which we’ll apply to the page in a simple `_layout.mote` file:

```
<!doctype html>
  <html lang="en">
    <head>
    <meta charset="utf-8">
    <title>
      {% raw %}{{ title }}{% endraw %}
    </title>
  </head>
  <body>
    {% raw %}{{ content }}{% endraw %}
  </body>
</html>
```

With a layout we can do a double render. One pass to render the page body and another pass to render the layout which includes the (rendered) page body. We’ll add the rendered body to the page hash as `content`.

```
def render_page
  Mote.parse(page.content, self, page.keys)[page]
end

def render
  @page[:content] = render_page
  Mote.parse(File.read("_layout.mote", self, page.keys)[page]
end
```

Let’s extract the duplication into a `mote` method:

```
def render_page
  mote(page.content)
end

def render
  @page[:content] = render_page
  mote(File.read("_layout.mote"))
end

def mote(template)
  Mote.parse(template, self, page.keys)[page]
end
```

Another way of doing it would be to render in a single pass and call out to the rendering method whenever the content gets called:

```
class Context
  …  
  def content
    render_page
  end
  
  def render
    mote(File.read("_layout.mote"))
  end
end
```

This way, content is only generated whenever it is called by the `_layout`. It’s really six and two threes. The only further improvement I would consider making in the second approach is to cache the rendering to ensure we’re not doing it twice.

Partials work on very much the same principle. In fact all three: layout, content, and partial are identical in operation and simply differ in scope. The layout nests the content and the content can nest partials. Since the render methods exist in the context they are all available to the templates.

In Sew I only have two differences to what is shown above. I handle (optional) localization of both templates and partials and instead of providing only the individual page data I include the full site context. This makes other pages available as well as site data and configuration. But the principles remain the same.

I hope it’s clear to see how straightforwardly these core ideas could be extended to support other use-cases. One that comes immediately to mind is the rendering of Markdown content.

By including a markdown parser like [kramdown][kd] we can simply add it to the rendering pipeline for the page body:

```
require 'kramdown'

class Context
  …  
  def render_page
    mote(markdown(page.body))
  end
  
  def markdown(text)
    Kramdown::Document.new(text).to_html
  end
```

Now we can write our post bodies in markdown and they will be rendered to HTML as part of the build. It really is that simple.

—*Monday 22nd February 2021.*

[mote]: https://www.crossingtheruby.com/2021/02/20/ruby-templating-with-mote.html
[sew]: https://www.crossingtheruby.com/2021/02/21/sew-for-static-elegant-websites.html
[kd]: https://kramdown.gettalong.org
