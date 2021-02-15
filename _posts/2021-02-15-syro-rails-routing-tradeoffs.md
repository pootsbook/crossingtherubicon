---
layout: post
title: Syro and Rails Routing Trade-offs
---

My [comparison of routing][cor] between Ruby on Rails and Syro raised some interesting themes that I’d like to explore. The primary theme is that of trade-offs. 

Although we are always, often subconsciously, concerned with trade-offs on a daily basis, it wasn’t until I came across Elm and listened to its creator Evan Czaplicki that I first became overtly conscious about their proper and explicit consideration.

This can maybe be seen as the result of maturation. As a young developer with a tool in my hand that gave me a substantial level of productivity and effectiveness the result can be zealotry. A triumph of your tool above any other tool. A blindness to the usefulness of other tools, frameworks, languages and an unwillingness to concede in the face of reason or opposition.

A wider range of experience along with the resulting well-roundedness allows you to see the limitations of the tools you use and the benefits of others in particular situations. You become more accepting of alternative points of view, and can increasingly view what once created conflict with a healthy regard to the benefits and drawbacks, now holding things in a healthy tension. You may even be prepared to accept that the tool you champion is, in some situations, not as well-suited as another.

You move from an absolutist point of view, “This is the _only_ way! And it’s the _best_ way!” to a deeper more studied analysis of _why_ one tool might work better in a given situation versus another. You begin to see the positives and the negatives of employing a particular programming tool or design approach in a given situation.

The thinking becomes more situational and you are weighing up alternatives. And that’s when we can talk about trade-offs.

The semantics of Elm are very much based on ML and Haskell. Evan Czaplicki wrote the Elm compiler in Haskell. Evan made the deliberate choice to exclude one of the features that many people familiar with Haskell feel is an essential tool in application design: type classes. Evan always frames the discussion in terms of trade-offs. Yes, you reduce boilerplate and yes you have more flexibility. But, you make the language more inaccessible to newcomers and with that flexibility you can shoot yourself in the foot. Elm is targeted at ‘foreigners’ to functional programming, JavaScript programmers, therefore type classes are out. 

Here we see a clearly stated goal, and the arguments being weighed up in relation to that goal. With other elements of the language it is the same.

This overt elevation of trade-offs and goals to the front and centre of the discussion is incredibly helpful and makes discussions about these kinds of things significantly more straightforward. It is then much easier to agree to disagree, but we do so in the understanding that the argument is a logical one and the conclusion fair to draw.

It also shows us that the choice not to do something is almost as important as, if not more important than, the choice to do something.

If we bring this around to the discussion of a routing DSL and URL helpers in Rails versus the routing structure of Syro and manual construction of URLs then it yields some interesting trade-offs. You can then decide if you feel the trade-off is worth it.

With Rails you have to learn a new DSL complete with a number of different methods for route definition.

With Syro you have a minimal set of primitives that impose no foreign DSL alongside the standard methods corresponding to an HTTP verb.

With Rails you have partial nesting of routes, but manual route definition is mostly at the top level.

With Syro each route is nested in the code at the same level it occurs in the URL.

With Rails the organization of route definitions in the routes file is arbitrary.

With Syro the organization of route definitions mirror the structure of your URLs.

With Rails you have to learn the rules surrounding the proper construction of the appropriate URL helper corresponding to a given route.

With Syro you have to construct the URL manually.

With Rails there is an automagic, implicit matching of a given route to a controller for dispatching the request, alongside methods and/or options to customize the dispatch target.

With Syro there is no dispatch, but you are free to do so explicitly as desired.

With Rails, these implicit definitions require less “glue” code to hook things together.

With Syro you are responsible for providing the glue.

With Rails you are able to use a helper method together with a model object and automagically generate a URL.

With Syro there are no URL helpers out of the box.

With Rails the resulting URL is obscured from view and opaque.

With Syro the resulting URL is front and centre and transparent.

With Rails you have more conveniences.

With Syro you do things manually.

With Rails you are subject to the conventions and rules of the framework.

With Syro, save conforming to the basic structure, you are free.

When the trade-offs are laid out like this, and maybe you can spot some more that I haven’t noted—or maybe you disagree with how they are laid out, then it is easy for you to make a decision as to what you would prefer as a routing tool. What do you find important? Does looking at this list make you rethink choices that you have made, or even make you question decisions that you made by default?

And one of the biggest trade-offs, for me at least, is lying right under the surface. I’ll look at that in a future post.

—*Monday 15th February 2021.*

[cor]: https://www.crossingtheruby.com/2021/02/13/framework-abstractions-http-rails-vs-syro.html
