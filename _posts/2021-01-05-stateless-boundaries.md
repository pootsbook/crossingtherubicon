---
layout: post
title: Stateless Boundaries
---

In [Functional Essence][fe] I mentioned that adopting a pure/impure separation in code allows for operating at a higher level of granularity. Distinguishing between pure and impure functions, modules, or code aids both the readability and the designability of software. However, the distinction itself also resembles the distillation of the essential and the accidental.

Gary Bernhardt’s “[Functional Core, Imperative Shell][fcis]”—worked into his treatise on “Boundaries”—discusses the benefits of keeping the core of your software functional and pushing imperative code to the outer shell. This is analogous to what a strongly, statically typed language like Haskell or Elm do at the language level. They lift the notion of side-effects into the type system, for example, `IO` or `Command` respectively.

In so doing, a clear distinction is made between functions with side-effects and functions that are pure or stateless; those that change the outside world in ways that are difficult to be repeated deterministically versus those that are deterministic, always returning identical output given the corresponding identical input.

Good design is said to be that which pushes the impure code towards the boundaries of the software, maintaining purity in a central core with a large surface area. Although it might be a stretch, and although we may have to add a filter to our lens, I believe this is also a tool to more precisely define essence and accident. In this particular scenario the accident corresponds to the unpredictability of the outside world and essence to the orderliness of the inner circle.  

—*Tuesday 5th January 2021*

[fe]: https://www.crossingtheruby.com/2021/01/04/functional-essence.html
[fcis]: https://www.destroyallsoftware.com/screencasts/catalog/functional-core-imperative-shell
