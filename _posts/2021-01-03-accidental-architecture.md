---
layout: post
title: Accidental Architecture
---

Yesterday I wrote about how frameworks embody the accidental in software development ([Fundamentals over Frameworks][fun]). I want to remark on one way that I believe we have (sub-)consciously realised this. The movement towards clean architecture is nothing other than an explicit admission that the web framework is accidental to our essential business logic. 

Cleanly separating these domains in our applications is an attempt to distill our thinking into two clearly defined buckets: the treatment of the (business) problem above the plumbing of the framework. Ports and Adapters or Hexagonal Architecture, or however you want to generalise this approach, manifests itself differently in various programming communities. 

For example, a framework like Elixir’s Phoenix introduced the ‘context’ as a first-class citizen in a nod to Domain Drive Design. James Edward Gray II and Bruce Tate took this even further with their layered architecture in [Designing Elixir Systems With OTP][otp]. 

In the Ruby world, Gary Bernhardt amalgamated his years of Destroy All Software screencasts, in which he often explored code detached from Rails, into his talk “[Boundaries][gb]”. Other communities will have similar tributaries informing the mainstream.

The diversity of thinking and the paucity of said thinking’s adoption is evidence that this is a tough nut to crack. There is a cogent argument to be made that Rails is successful precisely where others weren’t _because_ it tightly couples the framework with business logic rather than separating them. That deserves a treatment of its own, yet the common sense remains that there ought to be a division and that this division is beneficial.

If we view the issue through the lens of essence and accident then this is a perfectly logical conclusion to draw. The challenge is, as always, not deciding whether or not there should be a division, but deciding where precisely the boundaries should be drawn.

—*Sunday 3rd January 2021.*

[fun]: https://www.crossingtheruby.com/2021/01/02/fundamentals-over-frameworks.html
[otp]: https://pragprog.com/titles/jgotp/designing-elixir-systems-with-otp/
[gb]: https://www.destroyallsoftware.com/talks/boundaries
