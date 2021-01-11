---
layout: post
title: The Universality of Elm
---

<small>
  <em>
    Penned on 16th January 2016
  </em>
</small>

One of the things that has surprised me about Elm is its universality. I believe this will also lead to ubiquity. Surprised, because web languages or frameworks are often quite specifically targeted at the creation of web applications, they are particular. 

We have seen that Elm’s particularity with respect to the choices it has made for us, constraining us to a set of semantics; functional, immutable, typed, etc. that also intelligently limit us to the Elm Architecture. Now you would normally say that such a limitation would bring with it a curtailed set of applications, but it appears that the opposite is true.

Other languages make themselves available—or at the very least, potentially suitable—for a wide range of applications by providing limitless possibility through a certain malleability, but in doing so, defer the cost of making decisions about how to mould the language to the point at which the application is built. In other words, they offer a large toolbox where you rummage around trying to find the particular tool that you need for this job. When you find a screwdriver body, you also need to search for the correct head for the screw in question.

Elm, however, gives you a toolbelt that you can carry around with you, where everything has a precise place. You are constrained in your choice of methods to attack the problem or apply the language but it just so happens that what you have is widely applicable. Like reaching down for a Phillips or a flat head screwdriver, enough to finish the job in ninety percent of cases. Why carry around what you don’t need? You also find it significantly quicker than searching for pieces in the toolbox.

Now what Elm offers is a programming model, or a paradigm if you will, that is not necessarily new, but is now being brought to bear on a new domain, or better a new environment. Functional Reactive Programming is a better way to program GUIs than what we are curently doing. It also turns out the the FRP paradigm has a wide range of applications. Elm encapsulates the essence of FRP in a language that forces you to do it right (one of multiple instance of right, rather than one to the exclusion of all others). 

That FRP is great for GUIs is something we have known for a long time, if we had but looked. That it has a wide applicability is perhaps not so well known, but has been ably demonstrated by the nascent Elm community in the production of multiple games. There is a nostalgic attraction to many of these given their imitation and re-implementation of those games available at the advent of personal computing. Snake, the first game I played on the PC; Minesweeper, the guilty party for many a wasted moment.

And it is not only games and GUIs for which FRP is suitable: animation, operating systems and more. It could even be argued that the classic client-server request response model could be remodeled with FRP, reacting to requests with responses as the result. In this way, with its simple to understand and simple to implement flavour of FRP, Elm is well positioned to take advantage of this shift, and can accelerate it.

—*Monday 11th January 2021.*
