---
layout: post
title: "Operations as Data: HTTPPoison"
---

HTTPoison is a popular Elixir HTTP client, a library for executing HTTP requests. When using the library the HTTP requests can be put together in several different ways. At its core however is the `HTTPoison.Request` module containing the `%HTTPoison.Request{}` struct. This is yet another concrete example of using, transforming, and manipulating a data structure to encode an operation as data. In this particular case, it contains all of the information necessary to execute an HTTP request.

```elixir
%HTTPoison.Request{
  method: method,
  url: binary,
  headers: headers
  body: body,
  params: params,
  options: options
}
```

The data structure can be passed into the `HTTPoison.Base.request/1` function which then takes the data and executes the HTTP request. Much of the rest of the request section of the library is simply a litany of convenience functions for constructing this underlying data structure. But it is also explicit and can be constructed manually. I like the level of control that this gives, and I like that everything necessary for the request is contained in one place. It adheres to the functional programming and Elixir philosophy of making everything explicit.

By surfacing all of the details and collecting them in one place it keeps the important details front and centre rather than littering important bits of information throughout your application in different places. Take building a simple HTTP client for a third-party REST API as an example. 

The `%HTTPoison.Request{}` struct gives you a clear focal point for gathering everything that is necessary and you are free to construct it however you want. In Ruby, with `net/http`, making an HTTP request is more convoluted. Each of the individual elements for an individual request are dispersed across a wide range of methods and corresponding method parameters. Good luck finding the right combination for any given request.

Of course, there’s nothing to prevent someone using the same approach in Ruby, but a quick review of the popular HTTP clients reveals nothing similar. There is often a `Request` object which encapsulates all of the individual elements, but creating this object requires leaning on the config, alongside different method calls with options parameters.

There is a certain simplicity to using a clear, visible, easy to understand, and straightforward to manipulate data structure as the foundation for an HTTP request. Theoretically this could used as the basis for making requests with any HTTP client, similarly to how Ecto.Query’s data can be adapted to execute against different data stores.

—*Monday 18th January 2021.*
