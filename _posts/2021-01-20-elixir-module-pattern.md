---
layout: post
title: Elixir Module Pattern
---

When getting acquainted with Elixir I have come across a pattern which keeps popping up in both the language itself and important libraries within the ecosystem. I’m not sure if this is something unique to Elixir or if it is common to other (functional) programming languages. Perhaps it might be a natural inevitability of programming in a functional style like [pure/impure separation][pis] or [operations as data][oad].

The pattern is hiding in plain sight, perhaps being so obvious that you easily pass over it: a module defines a data structure, usually a struct, and the majority of all functions in the module operate on that data structure, taking it as the first argument in the module’s functions.

Bruce Tate colloquially calls this the “reducer” after a reduce (fold) function that takes a list and processes it down to a single result. In his analogy, the functions that operate on the central data structure are reducer functions, and they can be piped together as if individual steps in the overall reduce pattern. 

It has also been referred to as “token”-based programming. Token is an overloaded term, but in this specific case it uses a board game analogy; a token is the physical token on the board game which represents player in the game. As a player progresses, her position and status in the game changes, and these ‘state’ changes are reflected in the token, or, in other words, the token is representative of the state of the player in the game.

Or even more simply, just a “grab bag” of data that gets passed around the module allowing you to take things out and put things in.

At the language level the data structure is the primitive belonging to the module in question. For example the `Keyword` module operates on the `keyword()` type, a keyword list; the `Map` module operates on the `map()` type, a collection of key-value pairs.

At the library level it gets more interesting. Popular libraries have a primary data structure at their core. 

For handling requests in web frameworks like Phoenix there is the `%Plug.Conn{}` struct which models a connection to a web server as part of an HTTP request. The request-response cycle is nothing other than a manipulation of this data structure. The functions that are provided in this module operate by either fetching individual bits of information from the large data bag or putting other bits in.

In Ecto, both the `Ecto.Query` and `Ecto.Changeset` modules have corresponding structs which act as the focal point for the respective modules. All of the functions (and macros) in `Ecto.Query` end up transforming the `%Ecto.Query{}` struct before it is sent off to a repository for execution against a data store.

All of the functions in `Ecto.Changeset` act against the `%Ecto.Changeset{}` struct operating on the data contained within it. Filtering, casting, validating, and defining constraints are all achieved by using the provided functions to manipulate the `%Ecto.Changeset{}`.

If you squint long enough the pattern seems very similar to a class in object-oriented programming but with several crucial differences. 

A class (equivalent to a module) encapsulates data (equivalent to the module’s core data structure) and provides a bunch of methods for dealing with the instantiated object, from getters and setters to more involved state-changing methods (equivalent to the functions in a module operating on the core data structure). 

However, in a module, the data is open, transparent, and unencumbered whereas an object is designed to hide the data. The object holds mutable state whereas the module never modifies the data but simply transforms it, providing a new copy and leaving the old intact. Unless the class has been designed to cough up the data then the data in the object remains inaccessible whereas the module has no claims to the data, it is free as a bird.

Functional programming seems to declare that it’s more practical to access the data directly than to go through a middle man, and that it’s in everyone’s best interested to expose the data in the nicest way possible. This is what well-written Elixir modules do.

—*Wednesday 20th January 2021.*

[pis]: https://www.crossingtheruby.com/2021/01/15/elixir-ecto-pure-impure-separation.html
[oad]: https://www.crossingtheruby.com/2021/01/17/operations-as-data.html
