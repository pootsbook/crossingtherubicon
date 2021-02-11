---
layout: post
title: "Framework Abstractions: HTTP Routing in Syro"
---

In [Framework Abstractions: HTTP Routing in Rails][fa1] I looked at routing in Rails. We saw that it is heavily resource oriented using a REST-ful convention. There are also a wide range of helper methods to either modify the standard resource route definition or to match URLs from scratch.

## Syro

In this post I want to look at [Syro][syro]. Syro stands for “**Sy**mple **ro**uter”. And it certainly does what it says on the tin. It is a redesign of [Cuba][cuba] which is itself a nod to [Rum][rum], the “g**R**and **u**nified **m**apper” for Rack applications.

It is not itself a fully fledged framework but a routing library, although it can be used successfully in a framework-like constellation of libraries. It comes in at an astounding 386 significant lines of code (SLOC) and you can read the source code, complete with comments in about ten minutes. Grasping what the library is doing might perhaps take a little longer, but not much.

Syro doesn’t contain any resource-oriented conventions. It is centred around one core idea or principle: tree oriented routing<sup>1</sup>. In simple terms, URLs can best be modelled as tree data structures. This stands to reason given that the world wide web was first imagined as a document repository mirroring a computer’s file system which itself is a tree of folders and files. 

Syro is also a simple wrapper around Rack, a fairly transparent layer at that. A request is simply the `Rack::Request` object while `Syro::Response` conforms to the Rack specification (returning the standard `[status, headers, body]` response) and delegates in places to a few Rack methods.

Syro, as well as being built on top of Rack, also uses [Seg][seg] as its only additional dependency. Seg, at 82 SLOC, is also a simple library for ‘walking’ a path (like `/users/42`) while consuming and capturing **seg**ments of that path.

Syro provides no implicit mapping from a route to a controller, instead allowing you to write the application code for handling the request in the router itself or explicitly calling out to another class, module and/or method. This means that the block passed to the given route in Syro is essentially equivalent to the mapped controller action in Rails.

To take the resource-oriented URL structure from the previous post and rewrite it in Syro it would look as follows. I have added comments to show the equivalent action in a Rails controller.

```
Syro.new do
  on "books" do
    post do
      # create
    end

    on "new" do
      # new
    end

    on :id do
      get do
        # show
      end

      patch do
        # update
      end

      delete do
        # destroy
      end

      on "edit" do
        # edit
      end
    end
  end
end
```

We can make a few observations. Firstly, the structure of the code follows the structure of the URL:

```
Path           HTTP Verb
---------------------------------
/books         POST
├── /new       GET
└── /:id       GET, PATCH, DELETE
    └── /edit  GET
```

Secondly, there are only two kinds of methods to grasp here: `on` and the methods mirroring HTTP verbs (`get`, `put`, `head`, `post`, `patch`, `delete` and `options`).

Thirdly, everything is explicit. There is no magic DSL, simply a clear definition of the routes, mapping directly to the URL structure.<sup>2</sup>.

Fourth, Syro neatly handles dynamic segments, placing the value of the matching segment into the `inbox`, in the case above for a URL of `/books/42` then `inbox[:id]` would contain the value `42`. 

Fifth, since each route is nested, computation and the resulting variables from an outer segment are available to the inner segment. This neatly bypasses the need for something like a `before_action` filter to load a given resource for multiple routes. You could do the following:

```
on :id do
  book = Book[inbox[:id]]
  
  get do; …; end
  patch do; …; end
  delete do; …; end
  on "edit" do; …; end
end
```

`book` is then available to each of the routes in the same scope. Furthermore, if we wanted to ensure that we only proceeded with the flow if a book with that identifier existed then we could use the flexibility of `on`<sup>3</sup>:

```
on :id do
  book = Book[inbox[:id]]
  
  on !book.nil? do
    get do; …; end
    patch do; …; end
    delete do; …; end
    on "edit" do; …; end
  end
end
```

This last benefit demonstrates that Syro can evolve from a simple routing library for URLs to a branching library for your code. One use-case that comes to mind immediately is authentication or authorization. This can all be handled at the edge of, or entrance to, your application. You can do your gate-keeping at the gate.

In terms of routing there’s really not much else to add. There is a `handler` method allowing you to handle particular HTTP status codes with default behaviour. This can also be used as a filter if desired.

But that’s it. There’s more to learn about manipulating the request and the response, but that overlaps more with Rails’ controller functionality than the routing functionality. Perhaps I’ll treat that another time.

Syro is refreshing in its simplicity. It feels transparent. It feels faithful to URLs and their structure. It is straightforward to understand and thus to use. It may be a little more verbose, but there is no implicit magic, just explicit code.


—*Thursday 11th February 2021.*

[fa1]: https://www.crossingtheruby.com/2021/02/10/framework-abstractions-http-rails-routing.html
[syro]: https://github.com/soveran/syro
[cuba]: https://github.com/soveran/cuba
[rum]: https://github.com/leahneukirchen/rum
[seg]: https://github.com/soveran/seg.rb

<div class="footnotes">
  <ol>
    <li>
The Crystal port is called <a href="https://github.com/soveran/toro">Toro</a>—“<strong>T</strong>ree <strong>o</strong>riented <strong>ro</strong>uting”.
    </li>
    <li>
If I really wanted a DSL to help me write a one-liner and dispatch a set of resourceful routes to a given controller and action then it would probably be trivial to write for the base case. I’d create a Syro::Deck with a method called `resources` and spit out the code above with an extra line in each handler calling <code>BooksController</code> with the corresponding action.
    </li>
    <li>
      Or rather, the flexibility of <code>match</code> which <code>on</code> uses under the hood. <code>match</code> accepts a string (for static segments), a symbol (for dynamic segments), and an expression that evaluates to a boolean (for logic).
    </li>
  </ol>
</div>
