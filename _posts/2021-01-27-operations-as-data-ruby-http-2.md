---
layout: post
title: "Operations as Data: Ruby HTTP (2)"
---

Yesterday I attempted to port back a functional style of programming spotted in Elixir back to Ruby in [Operations as Data: Ruby HTTP][oadr].

Today I’d like to reflect on what I came up with: how it compares with what I had initially; how it compares to doing the same thing in Elixir; where it could be improved upon; what feels good and right and what doesn’t among other things.

## Transparency

I think one of the most obvious improvements is the clarity of the mapping from reading some 3rd-party API documentation to the method executing HTTP request.

Given API documentation that looks like this:

```
GET /activities/{id}
```

We can codify this in a method as follows:

```ruby
def fetch_activity(id)
  request({
    method: :get,
    path: "/activities/#{id}"
  })
end
```

It’s that easy. In steps:

```
GET /activities/{id}

# Separate method and path
GET
/activities/{id}

# Transform into Ruby types
:get
"/activities/#{id}"

# Add monikers
method: :get,
path:   "/activities/#{id}"

# Wrap it in a hash and method call
request({
  method: :get,
  path:   "/activities/#{id}"
})

# Name the method and expose arguments
def fetch_activity(id)
  request({
    method: :get,
    path:   "/activities/#{id}"
  })
end
```

It is a one-to-one mapping of the information in the documentation or the API specification and the code. It is transparent, see through. I know what each method is doing, and all of the information I need to understand it is right there in the same place, in the same data structure.

## Elegance

For the Ruby purist, the equivalent method in my previous implentation would probably win the beauty contest:

```
def fetch_activity(id)
  client.get("/activities/#{id}")
end
```

versus:

```
def fetch_activity(id)
  request({
    method: :get,
    path:   "/activities/#{id}"
  })
end
```

Of course beauty is in the eye of the beholder but we can try and apply some objectivity to this.

The former (Ruby) implementation is far more succinct. It’s a one-line method coming in at around thirty characters. The latter (Elixir-flavoured Ruby) is spread over four lines, could arguably be three or even one, but ends up at around fifty characters. 

The former (Ruby) maps closer to the documentation:

```
       GET  /activities/ {id}
client.get("/activities/#{id}")
```

Here it’s one or two mapping steps rather than the three or four shown earlier. I think more generally that this is a critical point people don’t often understand when confronted with Ruby zealotry. Ruby’s syntax is so terse that you’re often writing with the ultimate concision. There’s no fluff. If I were really showing off I could turn it up a level and remove the parentheses:

```
       GET  /activities/ {id}
client.get "/activities/#{id}"
```

When you get used to that then the Elixir-flavoured solution is ugly by comparison. Not enough signal, too much noise. Of course, it will depend on your aesthetic sensibilities. And this is where we get into trade-offs.

## Code as Data

Although I’m not using the phrase _code as data_ very faithfully here, this is a key difference in the two implementations. In the former the fact that the request is a `GET` request is encoded in the code itself and not the data: `get` is a method call. In the latter, everything remains data, `:get` is a value. This helps in the end-to-end perpescuity mentioned earlier and I think it’s also an advantage in higher level comprehension of the code. 

That is, in the former, we have to read from two ‘locations’ each time: the method call and the arguments. Although these remain side-by-side, it feels to me like there is a necessary cognitive switch to grok what are (in this particular example) two elements of the same whole. In the latter, these two related elements exist at the same level and in the same place. They are classified identically and understood immediately.

Another aid to understanding is the labeling of the data that is provided in the latter with the keys on the hash. We know that `:get` is an HTTP _method_ and that `/activities/…` is an URL _path_. I’m on the fence with this as maybe we’re teaching granny to suck eggs here.

What modeling the request as a Hash/Struct does give you is that higher level ease of comprehension. If we accept the `Request` Struct as our interface it unifies the whole library from top to bottom. Each method takes the same parameter. In the former we need to understand the methods and method signatures of each HTTP method. These are not uniform (because we want each to be as concise as possible, and only therefore include what is absolutely necessary).

This is where we can get into a good-natured debate about elegance and beauty. For one the former is more elegant because of its eloquence and expressiveness in the small, while for another, the latter is more elegant because of the explicit and obvious thread that is woven to join all of the disparate parts in the large. I can understand one method and one parameter and I understand the whole library.<sup>1</sup>

Looked at from that perspective, Ruby’s concision stands in the way of a certain concision of thought. I think this is where I have changed the most after encountering functional programming. From an aesthetic perspective I am of the opinion that Ruby is almost as close as we will ever get to perfection, but now I’m more willing to accept that the  benefits that data as a first-class citizen bring to the table outweigh the verbosity. That verbosity leads to a clarity that simplifies things on a conceptual level.

That unifying data structure, the `Request` struct is not only a connecting thread, but can also act as a seam. A friend<sup>2</sup> pointed out that I’m carrying out the request in the `Pipedrive` class and that this could even be split out to introduce another layer. That is, what if the `Pipedrive` class only returned an operation? That way, it’s more consistent with the pure/impure separation we have seen before.

So then, an “operation” would be the precursor to the request, using the same example:

```
def fetch_activity(id)
  {
    method: :get,
    path:   "/activities/#{id}"
  }
end
```

The configuration could also be merged before being passed onto another object or module that carries out the request. We have fully decoupled the (pure) Pipedrive API specification and configuration from the (impure) mechanics of making an HTTP request.

Now I hadn’t even been thinking along that axis but having it pointed out to me also highlighted the triviality of actually switching it around when the code is architected in this way. In the former Ruby example this would be nigh on impossible. Here, in the latter, it’s a few rote changes.

And finally on the data as code front, while it makes the craftsman’s stomach turn, with things architected around the data structure then the creation of a client library becomes so scripted that you could probably generate the code from an API specification. This is something I’d like to explore some more in the future. For example, if you agreed on the interface, you wouldn’t need to have an HTTP client in the conventional sense of an HTTP client library wrapping a third-party API. Instead you would have a bunch of API definitions returning operations that could be passed to a separate HTTP client outside of your code. Now one isn’t responsible for building the whole shebang including HTTP, but just for defining the API in a format the client can understand.

## Doubts

One of the things I’m in two minds about is the `Request` struct. The `Struct` isn’t a very popular type in colloquial Ruby and I’m struggling to work out whether the extra ceremony gives me enough benefits over and above a simple hash. Or even that I could make this into a simple class wrapping the data with some convience functions and maybe even validating the shape of the data. Or try a module and associated methods to accomplish the same thing in an extreme functional flavour. Something to explore on another day. There’s no doubt that an Elixir struct is far better suited here.

## Closing Thoughts

One thought that stands out above others is that similar to my experiences with Elm, learning about fundamental ideas (in this case by exploring the way other programming languages and programming paradigms accomplish the same things) will always shed a new light on what you have already been doing. And that you don’t necessarily need to jump ship to take advantage of those ideas, instead you can integrate them back into your tool of choice.

This was a lot of fun. These are the kind of thought experiments that I have been dissuaded from (by myself) as pointless and redundant. It brought me back to some of the joy I experienced when learning Ruby for the first time. Walking down blind alleyways because you could. No clock, no deliverable, just plain curiosity. And it wasn’t pointless, or redundant. I learned a lot. Maybe you did too. Let me know.

—*Wednesday 27th January 2021.*

<div class="footnotes">
  <ol>
    <li>
As an aside, although in this case it is either/or, I don’t think it has to be that way—Elm gives me a lot of both.
    </li>
    <li>
And not just any friend, but someone who knows a thing or two about Elixir, thanks <a href="https://twitter.com/wojtekmach">Wojtek</a>!
    </li>
  </ol>
</div>

[oadr]: https://www.crossingtheruby.com/2021/01/26/operations-as-data-ruby-http.html
