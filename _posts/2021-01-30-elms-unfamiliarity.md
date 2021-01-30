---
layout: post
title: Elm’s Unfamiliarity
---

<p>
  <small>
    <em>
      Penned on February 19th 2016. Elm was at version 0.16, before subscriptions greatly simplified the reactive model previously represented by signals.
    </em>
  </small>
</p>

I have mentioned on several occasions that Elm is straightforward to learn, that it has a small surface area, that conceptually there is a profound simplicity. I don’t however, want to give the impression that it is therefore _easy_. On the contrary, there is a lot about Elm that is unfamiliar and foreign. I do want to emphasise that it is worth it, even if you never use Elm. The reality is that the pace of change in front-end development continues unabated, there is seemingly always something else to learn, lurking around the corner. What is important in these shifting sands is not learning every available tool and framework, but given the variability, concentrating on the deeper conceptual ideas that underpin them.

By doing this you give yourself a bit of room to breath, permission not to learn everything, and a foundation that is independent of the implementation. Now, there is a necessity to learn a concrete implementation in order to understand the conceptual foundation and I’d like to argue that Elm provides the best way to do that for the widest range of concepts that we are now seeing emerge.

I believe Elm strips away a significant amount of noise so that what we are left with is the concept in its purest form—only signal. I discern that much of what is happening is simply the outworking of two important principles, and these aren’t in any way ground breaking, but almost all other concepts flow out from these: functional programming and reactive programming. These are also the two challenging steps in learning Elm, because Elm does not allow you to depart from them in the same way that you could with JavaScript. 

Before I dive into those I want to make sure that you are convinced that this is the case. Consider React, and the way in which Angular and Ember have followed in their second versions. What does the React framework and the surrounding ecosystem bring to the table? UI as a function of application state; the application of a stateless function, a central tenet of FP. Redux, a reducer function as a central means of advancing application state; a data structure operated on by functions, a key differentiator of of the functional style using the common reduce function, known well to all functional programmers. React Router, a means of serializing state in the URL; same URL same page, a variation on immutability or statelessness. If we go wider, Cycle.js, built on Rx.js, a means to invert control by focusing on streams of data and allowing them to trigger execution: Reactive Programming.

All of these point back to the conceptual underpinning and all of these concepts are present in Elm, and, in a cleaner and purer form. That is, in Elm they have been reduced to their essence.

Elm is a functional language, it puts you in the (comfortable) functional straightjacket. It is immutable by default, making any mutation explicit. The kind of language Erik Meijer calls honest. Elm is built for Reactive Programming with a Signal as a first order concept in the language. This results in a clean expressive syntax so that the only hurdle you have to overcome is the concept itself. More than mildly refreshing after a bowl of JavaScript soup. 

So, as I said, the difficulty in learning Elm is not Elm, but the conceptual jump you have to make to grasp the foundations and implications of what it is built upon. This can validly be called a paradigm shift, and, not just one, but two. You have to make the leap from object-oriented, state changing, object instantiated habits to thinking in data, transformations, function composition, and _at the same time_ moving from explicit control with callbacks and events to handing that control off to these Signals who take charge so that you are the one reacting to ‘instructions’. 

These jumps can melt your mind a little, partly because they come at once, partly because if you have no experience of either paradigm they will seem completely alien. You will feel stupid (at least, I did) because you have to park your knowledge of almost everything you know about programming at the door. You’re back in primary school, first year of college, at the start—and that is incredibly uncomfortable for someone who has in any way been moderately successful in what they do.

But something unexpected happens; as you become more comfortable, as you start to find your feet, you might start thinking, “This isn’t bad, why did I ever do things any other way,” or, “it fits, it works,” or “I can’t believe I can recognise my code after a few weeks.” There comes a point where it clicks and there’s not much more to learn. Of course there is always more to learn but its deeper rather than wider, it’s the things that arise as a result of applying your new knowledge to a different domain rather than more syntax or language features. The basics are in place and they don’t change.

This change in foundation means a lot of what you will do here is different to what you might do elsewhere, so prepare yourself. It’s not an easy climb to the top but it offers a splendid view.
