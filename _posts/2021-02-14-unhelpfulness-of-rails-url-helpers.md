---
layout: post
title: The Unhelpfulness of URL Helpers
---

As you can see in my post comparing [Syro and Rails routing][srr] there are a number of methods in Rails routing which have no equivalents in Syro because they concern the definition of URL helpers.

A URL helper in Rails is a method which can be used in views and controllers to generate a URL rather than typing it out manually. Here the helper is `book_path(model)` where the model implements a `to_id` method.

Rails (with ERB):

```
# URL helper
<%= link_to 'Book', book_path(@book) %>

# Manual
<%= link_to 'Book', "/books/#{@book.id}" %>
```

Syro (with [Mote](mote)):

```
<a href="/books/{{book.id}}">Book</a>
```

In the comparison above it’s clear that the benefit of a URL helper is minimal. The URL is relatively straightforward, so the basic HTML version one might use in Syro is both shorter and clearer; you know what exactly what the URL is going to look like.

Additionally, we are introducing a layer of indirection between the view and the resulting URL. In many ways it obscures the process of URL generation whereas generating the URL manually retains transparency.

Rails provides a handy command line aid to presenting URLs and their associated paths with `rails routes`. At the beginning of your journey on Rails this acts as a liberally consulted reference as you seek to choose the right magical helper incantation to get the URL you want. And, at the risk of embarrassing myself, it doesn’t stop there.

With Syro, the router provides a nested structure mirroring the URL, so the code itself becomes your “routes file” and also the documentation for it.

The argument could be made that with more advanced constructions, e.g. scoped or nested routes, or mapping to the right module/controller, the Rails URL helpers then come into their own.

### Scoped

Rails:

```
<%= link_to 'Book', admin_book_path(@book) %>
```

Syro/Mote:

```
<a href="/admin/books/{{book.id}}">Book</a>
```

### Nested

Rails:

```
<%= link_to "Book Review", book_review_path(@book, @review) %>

# Using `url_for`
<%= link_to "Book Review", url_for([@book, @review]) %>
```

Syro/Mote:

```
<a href="/books/{{book.id}}/reviews/{{review.id}}">Book Review</a>
```

The scoping has no obvious benefit. With nested routes I have introduced the `url_for` helper which simplifies the routing significantly. Assuming you follow conventions then this is able to work out the URL from the Rails models that you pass to it. This is a bit of ‘magic’ which has an obvious convenience.

## Thoughts

Although I can appreciate the convenience of Rails URL helpers in certain cases I am unconvinced that these represent a significant benefit over creating URLs by hand.

To make good use of the URL helpers you must learn an API and abstraction with a significant surface area. Get something wrong and you are knee deep in some gnarly code when debugging. 

Your URLs become opaque, hidden from view under these ‘helpers’ and you spend more time figuring out what helper to call together with the correct incantation rather than gaining a knowledge of your applications URL structure and simple constructing the URLs by hand.

You get further away from the metal, and indeed the relief of your application’s entry points. I would argue that the time spent in learning the Rails conventions surrounding URL generation could be better spent in gaining an intimate knowledge of the layout of your application’s endpoints and associated URLs.

Lastly (and this is by no means an exhaustive list) it steps away completely from HTTP (the path element of a URL) and obscures the underlying technology. This means that learning Rails, is learning Rails, whereas working with something like Syro/Mote, or even generating URLs by hand in Rails, keeps you in the same domain and allows for your knowledge to be transferable. 

Focus not on the framework’s abstraction, but on [the deeper thing it is trying to abstract][fa].

—*Sunday 14th February 2021.*

[srr]: https://www.crossingtheruby.com/2021/02/13/framework-abstractions-http-rails-vs-syro.html
[mote]: https://github.com/soveran/mote
[fa]: https://www.crossingtheruby.com/2021/02/08/framework-abstractions.html
