---
layout: post
title: 'CodeMesh Elm Tutorial'
subtitle: Poots Picks up Proficiency
---

I was fortunate to book a seat to the Elm Tutorial at the CodeMesh conference in London with Elm author Evan Czaplicki at the very last minute. Fortunate because there was space left but also because if I hadn’t left it to the last responsible moment then I would likely have booked a flight in from Amsterdam instead of the train through the channel tunnel. As it happened, thick fog had grounded most planes at Schiphol on Monday morning  so the only downside to the train was that I was forced to purchase a seat in first class.

The travel experience was just that, but more importantly it got me to London in good time for the main event. I was sitting beside someone else for whom the lure of Elm was also enough to only buy a tutorial ticket for the afternoon rather than for the full three-day conference activities.

It had promised to be a hands on tutorial and Evan did not disappoint. He had prepared a Git repository with examples and exercises and he guided us through those on the big screen. I had expected more time for a pattern of individual work and group review, but in hindsight observing Evan walk through the material with diversions for questions and explanations was a much more valuable use of our time. The exercises are still here to do at home and we covered a lot more ground in this way. Having read most of the Getting Started guide, the Elm Architecture Tutorial and the Pragmatic Studio course, the early parts were review but it was good to reinforce my delicate understanding and it worked well as a warm up to some of the more complex concepts later on.

Here, in no particular order, are some of the things I picked up.

### Modularity

I had raced through the Elm Architecture Tutorial and hadn’t quite appreciated that, in its eight steps, it teaches you all that you need to know about Elm architecture. This made me feel a lot better about not quite ‘getting it’ towards the later steps and gives me the assurance to go back and study the tutorial more slowly and methodically (without the nagging fear that I’m wasting time I could be using to learn *all the things*). The small surface area of Elm, its cognitive concision, is invigorating—and worthy of a post of its own.

What is most remarkable is that the Elm Architecture works just as well in the large as in the small. This had been one of my internal questions, “Sure, the Elm Architecture works for these simple examples but how does that—that probably doesn’t—scale to larger applications?”. That was emphatically refuted in the finest possible way: by code and demonstration. The Counter becomes CounterPair and on the train back I coded up CounterPairPair just to hammer it home. I haven’t yet had a go at a dynamic list of CounterPairs but I saw enough to know that it is an implementation detail.<sup>1</sup>

The modularity is enhanced by allowing you tight control over what pieces from your component you expose to the outside. I hesitate to use the overloaded term encapsulation to describe it, but that’s exactly what it is if you focus on the first-order definition rather than the derivative referent. Focused individual components compose well into the greater whole. If we define a component as the standard triad of Model-Update-View then it’s components all the way down: fractal design, Russian doll, whatever you want to call it.

The most enticing benefit is that you will only ever be dealing with at most three levels of abstraction at any one time. There is the level you are working at, and potentially the level above and/or below. That prospect is enough for me to lay down my multi-layered, tightly coupled, object-oriented tools.

### Functional (Reactive) Programming

I cut my teeth as a programmer with Ruby which is object-oriented to the core. Kent Beck is supposed to have said, “I always thought that Smalltalk would beat Java, I just didn’t know that it would be called Ruby when it did.”<sup>2</sup> Everything is an object, and practical object-oriented design is the written rule. In stark contrast, Elm is a functional language to its very core, and it is reactive, dealing with asynchronous data streams or signals as a way to better implement responsive GUIs.

The functional approach will be a departure from my norm, and I got a glimpse of this when we were ‘mob programming’ a solution for calculating the depth of a tree structure. Having dealt with an empty leaf node we began to think about how we might traverse the tree and calculate a count. I, with others, instantly began to fabricate a terribly complicated and convoluted function involving maintaining state and looping through the tree updating a counter, until someone shared the embarrassingly obvious-in-retrospect solution that every functional programmer will know: just add one and call yourself for both left and right nodes. Recursion.

The reactive part is also new for me and I’m glad that we took a trip into Signals and Tasks. Signals are a means of routing events into the core logic of an application using the Elm Architecture. The good news is that Elm handles a lot of that for you, abstracting away some of the complexity so you can focus on your application.

Tasks are Elm’s way of communicating with the outside world, e.g., HTTP or the browser’s LocalStorage APIs, and bear a similarity to JavaScript Promises. Mailboxes work in conjunction with Tasks to help keep the Elm Architecture unspoiled by routing things back into your application via Signals.

I feel like this is the area where I will need to put most of my learning effort.

### Elm’s Future

If I remember correctly Evan said he started working on Elm some time in 2011 for his senior thesis—and he doesn’t sound like he will be stopping soon. In the faddish and ephemeral world of front-end development, playing the long game may seem like a fool’s errand. I, for one, am very grateful for the tremendous amount of work that he has clearly put into Elm. When discussing the helpful, almost human, compiler error messages, Evan declared it was important to get these things right at the beginning if you’re going to be going years and years (paraphrase from notes). This meticulous attention to detail, both in language design and user experience  is why I think Elm will continue to go from strength to strength. 

It’s not quite there yet (wherever _there_ is), but it is incredibly close, and it will get there. It’s only a matter of time. A cursory perusal of Twitter will confirm that the reaction to encountering Elm is euphoric. And it’s not the hipsters who are cargo-culting but leaders in the thick of front-end innovation are recognising it as the most obvious thing they never thought of. And that’s when you know you’re onto a good thing. Elm is fast approaching the tipping point. 

### Miscellaneous

There were a number of other interesting tidbits that I picked up that I’ll jot down here and maybe fill out at a later stage:

- The Elm Reactor downloads packages automatically if you haven’t already got them installed. Sweet.
- Evan wanted Elm to resemble CoffeeScript when you open a file, with a focus on being friendly and approachable.
- In Elm, functions don’t have multiple arguments, those are just multiple (compiler optimised) functions with one argument.
- When pattern matching via Records, the pipe implies there might be other properties in there as well.
- Underscore, _, is a wild card.
- There is a `Debug` module which allows you to `log` values or `crash` the program with an error message.
- Using the term ‘stateless function’ is preferable to ‘pure function’.
- The goal for the type system is not to elevate types for types’ sake but to allow developers to achieve their goals more quickly by getting useful feedback from the compiler.
- Tasks and Effects help to extend the simple architecture without getting too complex.
- All templating languages tend to Turing completeness, so why not acknowledge that up front and take advantage of the fundamental abstractions that continue to be available (Elm’s ‘templates’ are written in Elm).
- The Elm Architecture allows you to test at the right level of abstraction.
- You _can_ develop without mutation.
- Communicate with the outside world via Ports.
- The potential for Elm to facilitate new and exciting ways to visualise your programs is inherent. A long-term goal is to _see_ your program.
- Elm is being used in production by a few companies.
- Elm makes refactoring more straightforward, i.e., if it builds, it likely works.
- It seems like Elm could be a bridge between the ivory tower and the coal face.

Many thanks to CodeMesh for making half-day tutorial tickets available, and most of all to Evan Czaplicki for a fantastic experience. The best recommendation I can give is that I hope to fly back for more, [tomorrow][elm-hack]. 

—*Sunday 7th November 2015*

<div class="footnotes">
  <ol>
    <li>
These building blocks are taken straight from the <a href="https://github.com/evancz/elm-architecture-tutorial/">Elm Architecture Tutorial</a>.
    </li>
    <li>
I couldn’t find the original citation but there are enough attestations from reputable sources to believe it is genuine.
    </li>
  </ol>
</div>

[elm-hack]: http://www.meetup.com/West-London-Hack-Night/events/226392298/
