---
layout: post
title: Sew with Hypertext
---

I wrote about [building a static site generator][sewm], [Sew][sew], with Mote as a templating library. Now that I have reached a templating API with Hypertext DSL that I am happy with I’d like to have a go at replacing Mote with Hypertext in Sew.

Sew is itself a single [97 SLOC file][sewc]. It contains a `Context` class which handles the rendering of templates with layouts and partials using Mote. I have reduced the snippet below to just the rendering aspects. (`@site`/`site`) is an `OpenStruct` object containing an array of _pages_ and an array of _data_ files.)

```
class Context
  …
  def render(page)
    @site.page = page
    @site.content = mote(page.body)

    partial("_layout")
  end

  def mote(content)
    Mote.parse(content, self, site.each_pair.map(&:first))[site]
  end

  def partial(template)
    localized = sprintf("%s.%s.mote", template, site.page.locale)
    if File.exist?(localized)
      mote(File.read(localized))
    else
      mote(File.read(sprintf("%s.mote", template)))
    end
  end
end
```

As a short refresher, the `Context` takes the site struct on initialization and then each page is rendered one by one with this site object as the params. The site object is modified slightly for each page with `@site.page` (the current page details) and `@site.content` (the rendered template from that page for inserting into the layout).

This is how the context is used as part of the `Sew::build` command:

```
context = Context.new(site)
…
site.pages.each do |page|
  File.open(page.destination, "w") do |file|
    file.write context.render(page)
  end
end
```

So basically one context object is used and each page uses the same object but with a different page. With Hypertext we’ll need one object per page so we can rejig things around a little:

```diff
- context = Context.new(site)
  …
+ site.layout = File.read("_layout.ht")
  site.pages.each do |page|
+   site.page = page
    File.open(page.destination, "w") do |file|
-     file.write context.render(page)
+     file.write ???
    end
  end
```

We set the layout globally, assuming one layout for the whole site. We know that with Hypertext we won’t need a double pass, so we can remove the assignment of `site.content` and rely on `site` with `site.page`. We assign `site.page` to the current page (defined by the loop).

And now we need to dovetail our Hypertext DSL Template class into our rendering flow. For now, we can ditch the `Sew::Context` and replace it with `Sew::Template`. 

We know we’re going to need the fragment rendering versus the file rendering, since we have already loaded the file contents into memory.

And since `site` is an OpenStruct we’ll need to use `OpenStruct#each_pair` instead of `Hash#each` to do the variable assignment.

(As an aside, this is a perfect example of knowing your tools well and knowing the problem space well since you can match the two easily. The alternative is to write a hodge-podge of decision logic to cater for all cases.)

```
class Sew
  …
  class Template < Hypertext::DSL
    def initialize(site)
      site.each_pair do |key, val|
        instance_variable_set(sprintf("@%s", key), val)
      end
      
      @ht = Hypertext.new
      render @layout
    end
    
    def render(string)
      instance_eval string
    end
  end
end
```

I’ve opted for `render` rather than `fragment` or `partial` here because I think it reads better in this context.

If we want partials then we’ll have to read from file explicitly (which we’re not doing with layout and template) so I’ll introduce the `partial` method to do just that

```
def partial(filename)
  render File.read(filename)
end
```

As you may have noticed I’m still not enamoured by Frankenstein’s method which switches on (non-)existence of a file.

And for those familiar with Sew I’m leaving localisation out for now to keep things simple.

If we now return to the `Sew::build` command then we can insert our `Template` class for rendering pages, replacing our `???` placeholder.

```
site.layout = File.read("_layout.ht")
site.pages.each do |page|
  site.page = page
  File.open(page.destination, "w") do |file|
    file.write Template.new(site).to_s
  end
end
```

Given a layout and template and a partial in Hypertext DSL (and Sew) form:

```
# _layout.ht

html lang: "en-GB" do
  head do
    title do
      text @page.title
    end
  end
  body do
    render @page.body
  end
  partial "_footer.ht"
end

# index.ht

---
title: Sew Me
---
h1 do
  text "Welcome"
end
p do
  text "A paragraph sewn together."
end

# _footer.ht

footer do
  text "You put your foot-er in it."
end
```

Then running `$ sew build`, lo and behold, produces the following output:

```
<html lang="en-GB">
  <head>
    <title>
      Sew Me
    </title>
  </head>
  <body>
    <h1>
      Welcome
    </h1>
    <p>
      A paragraph sewn together.
    </p>
  </body>
  <footer>
    You put your foot-er in it.
  </footer>
</html>
```

Wonderful. A full [gist][gist] shows all of the code together. Sew is down to 72 SLOC. I can live with that.

—*Thursday 8th April 2021.*

[sewm]: https://www.crossingtheruby.com/2021/02/21/sew-for-static-elegant-websites.html
[sew]: https://github.com/pootsbook/sew
[sewc]: https://github.com/pootsbook/sew/blob/master/lib/sew.rb
[gist]: https://gist.github.com/pootsbook/a0d684de1446eaec7e4dd38470e1722a
