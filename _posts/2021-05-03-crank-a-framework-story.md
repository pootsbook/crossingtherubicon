---
layout: post
title: "Crank: A Framework Story"
---

One of the themes that I’ve been trying to articulate on this blog is that we shouldn’t always assume that something has to be complex. And that this perceived complexity sometimes limits us, even if only in our own minds, from solving problems ourselves. We prefer to outsource the thought work without realising that adopting the thinking of another we end up thinking more than we would have thought if we had done the thinking ourselves.

One of the contributing factors in this sorry dance is that we are not well acquainted enough with the primitives that act as the building blocks for simpler solutions.

I came across the most wonderful demonstration of this principle after seeing an acquaintance, [Charles Lowell][cl], reference the genesis of Crank, a front-end JavaScript framework.

The author of the framework has two posts on the Crank website. One details the inception and rationale behind the framework and the other builds the framework step-by-step from scratch.

That the whole framework can be reproduced within the contraints of a blog post gives an indication of the simplicity that is at play.

What’s most interesting to me is the honest confession at the beginning of the first post, one with which I can strongly identify.

> I will be honest. Before embarking on this project, I never considered myself capable of making a “web framework.”
> —[Introducing Crank][ic]

In our minds we separate the authors of frameworks from ourselves, defaulting to the (understandable) assumption that if someone has written a framework they then belong in another, more premier league.

Then there comes a moment at which the Emperor is no longer wearing any clothes, and all it takes is a moment of lucidity to realise it, and then the courage to call it out.

> over time, I grew increasingly alienated by what I perceived to be the more general direction of React … each new API felt exciting, but I disliked how opaque and error-prone the concrete code written with these APIs seemed

Now, when one is unsatisfied with a JavaScript framework, in this case React, then one is spoilt for alternative choices. In this case, the author was not satisfied with how other libraries blindly cargo-culted React’s new features.

> Rather than thinking critically about each new feature, these libraries seemed eager to mimic them for purposes of compatibility … it was the API itself that needed fixing.

The author then goes into detail about how React’s Suspense API made no sense to him after diving into the details. This prompted him to relise that one of React’s fundamental assumptions, that rendering should always be ‘pure’, was in fact the source of a lot of complexity.

He then did some experiments on his own, without this dogmatic assertion, lifting that constraint. And then came the epiphany.

> The entire React lifecycle, all of the `componentDidWhat` methods, everything which React was trying to do with classes and hooks and state and refs, all of it could be expressed within a single async generator function.

Generators are a part of the JavaScript language and runtime. A knowledge of this primitive allowed Crank’s author to reframe the problem and arrive at a radically simpler solution.

> All the things which React required separate methods or hooks to accompish could be done within async generator functions with just the control-flow operators that JavaScript provides, and all wihin the same scope.

> For all the hard things that the React team was doing, a solution seemed latent within the JavaScript runtime. I just had to apply it.

And the key is that:

> In a sense, Crank is not “just another web framework,” but a design pattern which would eventually have been discovered by the JavaScript community anyways.

There is a sense of inevitability. That the solution is not designed but unearthed. A greater attention has been applied to the primitives, together with a critical return to the original goals.

This is a good example of arriving at simplicity in the wild.

—*Monday 3rd March 2021.*

[cl]: https://twitter.com/cowboyd
[ic]: https://crank.js.org/blog/introducing-crank
