---
layout: post
title: "Explicit over Implicit"
---

Similar to [operations as data][oad], there are other principles or patterns that we can say are encouraged by programming in a functional style. One of these principles is _explicit over implicit_.

Operations as data could itself be seen as a means of introducing an abstraction layer between two elements that would previously have remained invisible. It makes the operation explicit as data, rather than implicit between the lines of the code.

[Elm’s `Maybe` type][emt] is another example of making the potential absence of a value explicit, rather than letting it remain implicit. We can also consider Elm’s `Result` type as yet another example of this in the wild.

The separation of [essence and accident][eaa] in the [pure-impure separation][pis] is also a result of making the distinction between two categories of function or operation explicit rather than leaving it as an exercise for the reader. A function is explicitly designated as stateless (pure) or having side-effects (impure). 

In the [structure of an `Ecto.Changeset`][ecs] we can also see this pattern. All of the data necessary to capture the change operation on a given data structure is given an explicit label and stored in the struct: the `types` for casting, the original `data` for comparing, and the `changes` for validating and applying. This is in contrast to an implicit design where you might work backwards from a comparison of old and new data to figure out what the specific changes were.

When you go from the language level to the framework level you can also discern the principle arising from the language to influence framework design. Elixir’s Phoenix is famously the “no magic” framework where everything is explicit and is often presented in contradistinction to Ruby’s Rails’ implicit magic.

I see the general principle as an extension of what is termed one of the two hardest problems in Computer Science, naming things.<sup>1</sup> Here in the sense of giving a previously (implicit) concept an (explicit) name; recognising the existence of something and classifying it.<sup>2</sup>

Functional programming makes it easy to see what is not there. To speak it into existence. Perhaps this originates from the choice to make data a first-class citizen rather than hiding it in an object’s state. There are fewer convenient affordances to get at and transform the data through the object leading you to different abstractions. There’s nothing hiding away. It’s more verbose, but at least you know what precisely is going on.

—*Tuesday 2nd February 2021.*

<div class="footnotes">
  <ol>
    <li>
      The other problem is cache invalidation and off-by-one errors.
    </li>
    <li>
      The other, arguably more challenging, aspect of the naming problem, and the one that is often primarily referred to in the telling of the joke is deciding what the name should be.
    </li>
  </ol>
</div>

[oad]: https://www.crossingtheruby.com/2021/01/17/operations-as-data.html
[emt]: https://www.crossingtheruby.com/2021/02/01/maybe-elm.html
[eaa]: https://www.crossingtheruby.com/2021/01/02/fundamentals-over-frameworks.html
[pis]: https://www.crossingtheruby.com/2021/01/05/stateless-boundaries.html
[ecs]: https://www.crossingtheruby.com/2021/01/24/what-is-a-changeset.html
