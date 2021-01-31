---
layout: post
title: Reactivity in Elm
---

<p>
  <small>
    <em>
      Penned on February 20th 2016. Elm was at version 0.16, before subscriptions greatly simplified the reactive model previously represented by signals.
    </em>
  </small>
</p>

Elm is a language for functional reactive programming. I’d like to break down a few things contained in that statement before considering reactivity in Elm.

The first is that Elm is functional, that is, a language that helps (and forces) you to write in the functional style. Things like immutability, first class functions, lists etc.

The second is that Elm is reactive, that is, the programming model reacts to values that change over time, modelled by a `Signal`.

And the third and final aspect follows from the first two, that Elm is functionally reactive, that is, its reactivity is constrained within the functional framework in which it is embedded. I think it is also helpful to say that reactivity is the logical outworking of a purely functional style operating in a world that advances (changes) by the tick of the clock.

What do I mean by that? Functional programming is a way to model the world in a framework that attempts to be faithful about as much as possible in the real world. It is acting as a bridge between the mind and the machine with the most signal (as opposed to noise), breaking things down to a simple, elegant, mathematically sound model. (Part of the reason one can be intimidated by functional programming is the presence of mathematics and the disparaging and supercilious attitude that is often exhibited by those who have ‘climbed the mountain’.) What I mean in practice is that immutability and statelessness constrain you to think about problems in a way that is consistent with the rudimentary rules of algebra, e.g., swapping a function call with a value and enjoying the confidence that comes from knowing that nothing will break. Constricting, but powerful.

Now if values are immutable then how on earth do we actually accomplish anything? The usual answer is with side-effects. But the problem when attempting to model the world faithfully is that you need to trigger the side effects yourself. And that is imperative and not declarative. Time is the biggest problem here since it is always changing. If anything relies on time then this is a subtle source of mutability, impurity, and statefulness—and worst of all, it is implicit.

The goal of functional programming is to make the model of the world explicit. Rather than bake in mutation, model mutation explicitly and push it to the edge of your program with side-effects. However, this is not a very effective way to deal with time, because time is an input to the program rather than an output. The most explicit way then to effectively model a mutable input like time in a functional style or program is to integrate it into the language as a first class concept where it can be modelled consistent with the rules that are already in place. This is why `Signal`s exist.

A `Signal` is simply a value that changes over time. This is the source of reactivity in Elm. In essence it provides the machinery for a functional application to step into a new valid state without compromising on any of the characteristics that make it functional. 

When I discovered this it blew my mind. Why? Because it is so simple, it is so powerful. I get all the benefits of functional programming _in the real world_. Things that previously would have been considered stateful and required side-effects can now be modelled explicitly as values that change over time. This doesn’t eliminate the need for side-effects, but it does improve the situation dramatically.

With this power comes great (no, not responsibility, that’s the computer’s job) bending of the mind. This reactivity brings inversion of control. Sorry Chuck, you’re not in the hot seat any more—the changing values are, the `Signal`s. That is, anywhere you declare a `Signal` in your application is now a source of change and a trigger of execution. Easy enough to say, difficult to throw out all previous mental models in favour of this minimalist masterpiece.

It’s like the life-changing magic of tidying up _your brain_. It might sound alarming at first, execution is being triggered in all these random places and there’s nothing I can do to bring it all under control, but really, because your application is functional and therefore declarative, you’ve _already_ dealt with all the possible permutations and the compiler has rubber stamped it.

A functionally reactive program then is simply a set of instructions to carry out when a value, or values, change. The beauty is that then things that change can be modelled in this way. User input via a mouse click on a button is handled by the observation of the `Mouse.clicks` signal which is mapped to an actions signal that is handled in an update function that produces a new value for the model signal which is handled in the view function to produce a new value for the Html signal displaying your application and ready to start all over again. It really is that simple.

Although it took me a while to grok. I didn’t get frustrated because I had prepared for the fact that I wouldn’t get it first time round, or second time round, etc. And, to be honest, none of this is a prerequisite to actually using Elm. All of this is abstracted away for you in `StartApp.simple`. I’m just the curious type.

The regular `StartApp` adds effects into the loop but the conceptual simplicity remains. It’s just functions and values. Changes in values being handled in the same way you would handle raw values themselves. Everything is hooked up for you automatically. I continue to be amazed both at functional programming and how accessible Elm makes it for encountering and implementing these ideas.

—*Sunday 31st January 2021.*
