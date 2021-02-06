---
layout: post
title: SOLID Ecto Changesets
---

So far in [What is a Changeset? ][wic] and [Validations][wicv] we have seen how an Ecto Changeset is an immutable data structure which is transformed by applying different kinds of functions for different purposes.

The first thing that strikes me about this is that a changeset is responsible for several different things. Certainly these are all united by the goal of preparing, controlling and executing a set of changes but I can’t help but get the feeling that it is doing too much.

Doing too much was always a criticism of ActiveRecord in Ruby on Rails. Indeed if we trace the heritage of Elixir in general and Phoenix in particular then there is a strong current of being _better_ across a chosen set of criteria. 

Elixir borrows Ruby’s friendly syntax but inhabits a world of performance, concurrency and fault tolerance which Ruby finds difficult to match.

Phoenix is a framework which supposedly learns from the limitations of Rails to bring something that is explicit (over Rails’ implicitness), straightforward (over Rails’ indirection) and simple (over Rails’ complexity). In addition of course to the introduction of a programming model that fits hand in glove with the requirements of the web (`Phoenix.PubSub`, `Phoenix.Presence`, `Phoenix.LiveView`).

One of the claims is that ‘Rails is not your application’ with Phoenix building on this [Clean Architecture][ca] mantra by separating out `lib` and `web` folders and functionality, introducing contexts, and encouraging a more decoupled application design.

Changesets therefore provide an interesting litmus test of this philosophy. In other parts of Phoenix this clean separation of responsibilities can be clearly seen. Plug offers a very elegant way of handling the request-response cycle. Ecto.Query is a beautiful, thin layer of abstraction over SQL. With changesets on the other hand I’m a seeing a forest because of all the trees.

This could of course be down to my lack of experience and the resultant failure to fully appreciate the abstraction. It could be simply that this is a thorny problem to solve and dealing with different responsibilities is simply a fact of life. It could be that there’s something to be improved here, despite how embedded changesets in the mindset of a Phoenix developer.

Here are some of the ‘trees’ I notice:

- While changesets have been successfully decoupled from forms on the one side and queries on the other there remains evidence of a previous coupling: `action`, `repo`, `repo_opts`, `constraints`.
- Changesets are responsible for casting, filtering, validating, change tracking, and receiving feedback from the data repository. It feels like a lot.
- It feels in a way that a changeset is trying to operate on two different levels: the low-level change tracking, validating, etc.; and the higher-level encoding of the overall process or flow of data through the application from request to repository.
- My gut feel is that although this represents an improvement on the coupling of ActiveRecord, it feels like it didn’t go far enough.

Changesets don’t seem to comply with the Single Responsibility Principle, the first of the SOLID guidelines. It doesn’t decompose as neatly as I might like. It feels like it has been caught between two opposing principles and has thus been pulled in two different directions.

Changesets want to offer welcome convenience in building web applications _and_ they want to follow the Elixir and Phoenix principles of separation of concerns and decoupled code. These two goals seem to be in tension.

This post at least provides a line in the sand that I can revisit as I build up more experience in using and observing changesets in the wild.

Do any of these points resonate with you as a more experienced Phoenix developer? Do you strongly agree or disagree with any of my observations?

–*Saturday 6th February 2021.*

[wic]: https://www.crossingtheruby.com/2021/01/24/what-is-a-changeset.html
[wicv]: https://www.crossingtheruby.com/2021/01/25/what-is-a-changeset-validations.html
[ca]: https://www.crossingtheruby.com/2021/01/03/accidental-architecture.html
