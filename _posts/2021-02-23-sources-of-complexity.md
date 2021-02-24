---
layout: post
title: Sources of Complexity
---

While I may have railed against Rails and Jekyll in previous posts I wouldn’t want to give the impression that this comprises a flat-out rejection of bigger, more complex tools. In the day job our monolith is an eight-year old Rails application and this very blog continues to be built with Jekyll.

I hope to write up in more detail some of the refactorings I touched on in my talk “[Rediscovering Ruby][rr]” in at the Paris Ruby Conference in 2020 which detail how some of these explorations in simplicity yield major benefits that can be used alongside and within that bigger context.

When writing posts on the [architecture][sewa] and [rendering][sewr] of my simple static site generator, Sew it prompted me to consider a couple of different sources of complexity in bigger frameworks.

I think the biggest benefit of developing something from scratch is that you can see the core idea and structure more clearly, in a way that you will not discern or discover when reading the source code of a more complex approach. This was the premise behind one of the most helpful Rails learning resources that I had the pleasure of following, [Owning Rails][or] by Marc-André Cournoyer.

The reason that picking apart a framework is difficult is another instance of the accidental-essential split that I have considered in the past. That is, the core idea and structure is the essence of the problem, the skeleton, the bare bones. It is a walking skeleton, to coin a phrase from Alistair Cockburn, but in order to fully offer the flexibility and wider applicability for which a framework is targeted it must put on a lot of flesh. Some of that is muscle but in my opinion there is an overabundance of fat. This is the accidental, the cruft that accrues as a result of trying to be everything to everyone.

It’s really a catch-22, for a framework to be successful, seen through the lens of popularity and usage, it must appeal to a broad base. To appeal to a broad base it must cater for multiple different use-cases. To cater for multiple different use-cases the code must be flexible enough to handle them all.

The framework also becomes the victim of its own success. Given wider usage the peculiarities of operating systems, of development and production environments, of file systems and the inevitable encountering of unforeseen bugs will all necessitate a better handling of these edge-cases.

Taken together I see these as two of the main sources of complexity, both largely accidental and orthogonal to the problem space. Code being code it’s not possible, or at least hugely impractical, to try and separate these concerns from each other, and thus the clear water is muddied, obscuring the core.

Let’s take the example of markdown parsing and rendering. There are several markdown libraries in Ruby. Each of these markdown libraries can also be configured in a multitude of different ways. Custom markdown extensions, different flavours of markdown, specific rendering instructions, these all require some sort of interface or means of configuration if you include them in your project.

The safe option is to leave that complexity outside your framework. However most people expect the framework to be “batteries included” and to do that work for them. So it offers conventional defaults, and allows you to configure.

Our simple `markdown` method from [rendering in Sew][sewr]:

```
def markdown(text)
  Kramdown::Document.new(text).to_html
end
```

immediately becomes more complex. For we are now not seeking to render a markdown text to HTML using a fixed library with fixed options, but we need to abstract the rendering into something more flexible enough to handle multiple markdown libraries. We need to write some kind of adapter.

```
def markdown(text)
  MarkdownRenderer.new(text, :kramdown).to_html
end

class MarkdownRenderer
  def initialize(content, renderer)
    @content = content
    @renderer = renderer
  end
  
  def to_html
    case @renderer
    when :kramdown
      Kramdown::Document.new(@content).to_html
    end
  end
end
```

It immediately introduces a layer of complexity into the design. It also introduces another level of complexity in the sense of everything that could go wrong: no matching processor, dependencies not loaded, wrong configuration settings, you name it. And so code will be written to deal with all of that.

Take a look at how Jekyll handles markdown as one of what they call [converters][jc] in [`Jekyll::Converters::Markdown`][jcm]. There’s a lot there.

And so these are then choices you have to make. The best frameworks  hit a sweet spot on the spectrum of complexity and usability. They will never however be as simple as putting together your own targeted tool.

When I started out I would have had no idea how to build my own tool. Frameworks were a welcome aid to accomplishing what I needed. As I have grown in maturity and experience, when I start to hit a wall with a framework then I consider whether I really need to keep butting my head against it, or be more gracious to myself, take a step back, and think about how I might solve the problem without the framework’s constraints and complexity.

Have you ever found yourself fighting with the framework, thinking to yourself, there must be a better way? Take a moment to consider what that way might be. You’ll thank yourself.

—*Tuesday 23rd February 2021.*


[rrb]: https://www.youtube.com/watch?v=s5A4recPPC4
[sewa]: https://www.crossingtheruby.com/2021/02/21/sew-for-static-elegant-websites.html
[sewr]: https://www.crossingtheruby.com/2021/02/22/rendering-with-mote-in-sew.html
[or]: http://owningrails.com
[jc]:  https://jekyllrb.com/docs/plugins/converters/
[jcm]: https://github.com/jekyll/jekyll/blob/c9c9dc7dac74bce963101dd5c8670226dae01d85/lib/jekyll/converters/markdown.rb
