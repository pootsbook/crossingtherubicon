---
layout: post
title: 11ty Simplicity
---

In yesterday’s post, answering the question [Why 11ty?][w11], I mentioned that 11ty was simple. In this post I want to look at a couple of concrete examples of this.

In my posts I have talked about good abstractions are often those that are faithful to the underlying technology or concepts. For example, [Hypertext][ht] could more accurately described as HTML written in Ruby rather than a templating system _per se_.

If we think about a website then, for historical reasons, it takes its form and shape from a computer’s file system. The first website was actually the exposition of a local file system to a network on the world wide web. Over time, this one-to-one mapping of a URI to a specific file sitting on a server’s file system was replaced by applications which respond to requests dynamically. The applications don’t fetch a static file, but put data together with a template and render a file which is then sent back to the client.

Despite the dynamic nature of these generated pages, they, by and large, continued to follow a similar hierarchy to static file systems given that the URL or address by which a resource was located was still dictated by principles that had arisen from file systems (text separated by a forward slash with the implicit (inherent) parent-child relationship).

The move back from dynamic sites to static sites for content heavy websites was in part a realisation that it would be much more efficient to pre-generate pages that didn’t change much rather than generating them on the fly each time.

But in the transition from dynamic to static, static site generators did not always follow the same mental model of files in a directory mapping to files on the site. 11ty does.

11ty has “convention over configuration” routing. Rather than needing to define a configuration of how to route a particular file to it’s place in the built site it operates on the principle that the location of the file in the file system will be the location of the file on the website. It is easy to change of course by defining a `permalink` in the frontmatter, but it works ‘correctly’ out of the box.

This enables a zero configuration site. Given this sensible default which fits the mental model, we can be up and running with 11ty without any configuration, and in no time at all. In fact, you could run 11ty inside a directory of HTML files and it would produce a near identical site build.

Another element of the simplicity is that 11ty knows quite clearly what it is not. It is not trying to be all things to all men. It rejects client-side JavaScript as an element of the framework itself in contrast to a tool like Gatsby (which feels more like a client-side framework on the server-side). That’s not to say that you can’t make your own arrangements for client-side JavaScript, but they don’t form part of the core 11ty conceptual basis.

Through judicious choices, comprehensible defaults and simple concepts 11ty is much simpler than many of its peers.

—*Wednesday 19th May 2021.*

[w11]: https://www.crossingtheruby.com/2021/05/18/why-11ty.html
[ht]: https://www.crossingtheruby.com/2021/03/14/hypertext-write-html-in-ruby.html
