---
layout: post
title: Segmenting URLs with Seg
---

When discussing how Syro implemented its [routing DSL][srd] I mentioned its use of a library called [Seg][seg]. Seg is a simple **seg**ment matcher for paths, where a path is defined as a string starting with a slash (`/`) and containing segments separated by slashes (`/books/42`). It is perfectly suited for working with URL paths.

The library, calling in at 82 significant lines of code (SLOC), is focused and targeted at this one use-case and as such provides a perfect foundation for the functionality we saw in Syro.

Indeed, it hardly needs explanation, studying the code will bring everything to light. Nevertheless I thought it useful to give a more ‘literate’ overview.

The foundational data-type is a single String, and this is treated as a list of characters. The forward slash, `/`, carries special significance as a means of separating the string into segments.

A “seg” or segment object carries the path itself `@path`, the length of the path string `@path.size`, a helper storing the initial position (the index of the first character in the string), `@init = 0`. The position is then where most of the work is done. This tracks the current position of the path traversal. At the beginning this is the start of the string `@pos = 0`.

The position is what is going to be doing most of the work and the element that changes the most as you walk the path.

The `curr` method lets you know where you are in the path, and at the beginning will return the full path.

```
seg = Seg.new("/books/42")
seg.curr  #=> "/books/42"
```

To move down the path you _consume_ a segment which moves you along:

```
seg.consume("books")
seg.curr  #=> "/42"
```

You are now able to look back at the _previous_ segment:

```
seg.prev  #=> "/books"
```

You can continue walking down the path in this fashion until you get to the end. If you decide you want to walk backwards then this is also possible:

```
seg.restore("books")
seg.curr  #=> "/books/42"
```

This gives us a way to walk forward and backwards but it does assume that we know what the path is before we walk. In order to walk blindly we can _extract_ segments:

```
seg.extract #=> "books"
seg.curr    #=> "/42"
```

Extract both returns us the value of the segment _and_ it walks the path.

Like the `consume`/`restore` pair, `extract` also has a corresponding reversal in `retract`:

```
seg.retract #=> "books"
seg.curr    #=> "/books/42"
```

Importantly, if we want to _capture_ information from a given segment in order to use it for another purpose (think using `42` as an ID to later look up a record from a data store) then we can do that too. All we need to do is to provide a store in the form of a Hash alongside the name of the key we’d like to use for the data:

```
store = {}
seg.extract              #=> "books"
seg.capture(:id, store)
store                    #=> { :id => "42" }
```

And that’s pretty much all there is to it. Additionally the `root?` method will tell you whether or not you are at the root of the path (defined by a forward slash `/`) and `init?` will tell you if you are at the starting or _initial_ position in the path.

## Seg in Syro

In the calling of `Syro::Deck::API` a store is initialized, named the inbox, and a path is created by instantiating an instance of `Seg` with the path provided by Rack:

```
@syro_path = Seg.new(env.fetch(Rack::PATH_INFO))
@syro_inbox = inbox
```

When running the application the elements of the path are stored in environment, along with the inbox itself. This way the application keeps a record of where in the path it is and what values have already been captured.

```
env[Rack::PATH_INFO] = @syro_path.curr
env[Rack::SCRIPT_NAME] = script + @syro_path.prev
env[Syro::INBOX] = inbox
```

`Syro::API::Deck` then simply delegates the `consume`, `capture` and `root?` methods to their Seg counterparts also exposing the Seg object as `path`:

```
…
def path
  @syro_path 
end
…
def consume(arg)
  @syro_path.consume(arg)
end

def capture(arg)
  @syro_path.capture(arg, inbox)
end

def root?
  @syro_path.root?
end
```

These are of course then called upon by the `on` and `match` methods which I have described in my previous look at [Syro routing][srd].

This is incredibly elegant in its simplicity. It’s difficult to describe how this makes me feel. It is the very definition of distillation; the boiling away of extraneous material to arrive at the essential core. And it can be fully explained in a blog post. The code can be read in a coffee break. This to me is faithful abstraction. And it costs almost nothing.

—*Friday 19th February 2021.*

[srd]: https://www.crossingtheruby.com/2021/02/13/framework-abstractions-http-rails-vs-syro.html
[seg]: https://github.com/soveran/seg.rb
