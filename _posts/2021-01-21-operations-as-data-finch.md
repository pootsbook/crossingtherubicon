---
layout: post
title: "Operations as Data: Finch"
---

After reading my post on [HTTPoison][hp] a friend pointed out that the Finch library exhibited many of the same characteristics relating to operations as data.

Finch is a relatively recent, performance focused HTTP client written in Elixir. It can be used as an alternative to HTTPoison.

It has also opted to use an intermediate data structure for encoding the request in an almost identical fashion to HTTPoison:

```
                    # HTTPoison
%Finch.Request{
  body: term(),     # body
  headers: term(),  # headers
  host: term(),     # url
  method: term(),   # method
  path: term(),     # url
  port: term(),     # url
  query: term(),    # params
  scheme: term()    # url
}
```

It does two things slightly differently. Firstly, instead of a generic URL, it breaks the URL down into its constituent parts: scheme, host, port and path. Secondly, it removes the options keyword list from the request struct, opting to keep the options distinct from the request itself.

(Separating out the options makes sense to me, as they often govern the behaviour of the client when making the request rather than relating specifically to the request itself. I guess you could see this as another [essential-accidental split][fof].)

Again, the `%Finch.Request{}` struct can be created manually or via a helper function (`Finch.build/4`), similar to HTTPoison’s convenience functions.

And importantly, the building of a request struct is handled as a separate and distinct step to actually executing the request. The HTTP operation is first modeled or encoded as data. Then the request is made.

```
Finch.build(:get, "https://hex.pm")  # 1. Build request struct
|> Finch.request(MyFinch)            # 2. Make a one-off request
```

One of the interesting things about this is that if you want to stream the request rather than doing it all in one go, you could use the same request struct, but just pass it to a different function.

```
Finch.build(:get, "https://hex.pm")  # 1. Build request struct (identical)
|> Finch.stream(MyFinch, acc, fun)   # 2. Open a persistent, streaming connection
```

Another benefit of having these operations as data is that the the request struct abstraction can function as a primitive adapter. If, for example, you wanted to switch your HTTP client from HTTPoison to Finch then there is a built in seam requiring only a few changes rather than having to rip out a large part of your code and deal with the resulting trauma.

*Thursday 21st January 2021.*

[hp]: https://www.crossingtheruby.com/2021/01/18/operations-as-data-httpoison.html
[fof]: https://www.crossingtheruby.com/2021/01/02/fundamentals-over-frameworks.html
