---
layout: post
title: All Things to All Men
---

Some software tries to be “all things to all men” and I believe that this is one of the many causes of software complexity.

In particular libraries are often the culprit because a broader appeal leads to broader usage which brings (it is maintained) a virtuous circle of improvement.

In terms of a library’s complexity it can be a vicious circle.

I noticed this on a number of counts over the past couple of days.

When realising that templates could potentially be passed in as a string or as a file in a [previous post][rnt] then the natural response is to build in the functionality for both. 

Then all of a sudden we want to pass in both a layout and a template, and it could either be a file or a string. So we double our range of possibilities in one fell swoop. And what if we pass only one and not the other? Then we are going exponential. 

We suddenly have to cater for a range of edge-cases that rely on _hypothetical_ use-cases. And the hypothesis is based on how someone _might_ use the library more than how someone would.

Granted, someone might use the library with all of those possible combinations, but often they’ll use one particular configuration.

My hypothesis on the other hand is that if you stop trying to be all things to all men, and instead focus on making the principles and ideas behind the software visible—achieved via simplicity—then we can trust people to modify it to their use-case since they understand precisely what they are doing.

It’s at this point that I realise I’ve ranted about this before, and the title is almost identical, [Sources of Complexity][soc], so it’s at least now clear, if it wasn’t before, that this is a rather deep rooted conviction. Nevertheless my recent experiences compel me to write.

The above example is in the small. It’s one method, a set of parameters, switching between different options.

I came across an example at a larger scale in [Sew, yesterday][sy]. Well, actually both small and large in one.

I opted for both rendering from string _and_ from file, but, given my use-case, it didn’t cause any complexity. A layout is a given, both template and layout will be rendered from a string, and a partial will always be read from file. There are no branches. This comes from a specific use-case which is bounded. And my knowledge of how Hypertext works, based on its simple implementation, means I can shake the solution out of my sleeve. It’s 15 SLOC and half of that is boilerplate.

In the large, I was able to replace the rendering engine in Sew, Mote, with Hypertext because Sew is also simple. No configuration, just judicious application of a pair of code scissors, a needle and some thread. Because the seams are straightforward, perhaps we could call them single-threaded—the Dutch _eenvoudig_, literally single-threaded, translates to _simple_ in English—then it’s not a lot of work.

Should we be dealing with something complex, or multi-threaded, interwoven, then we’d have to disentangle.

And I believe that this process of rewiring a couple of wires versus plugging into a multi-faceted adapter is facilitated when you allow the fundamental problems to surface in the code. Given an understanding of the fundamentals you increase confidence in the act of modification. Open the adapter and you swiftly close it again while calling the electrician.

I want you to hook up the wires, not to have to rely on an electrician. And not to have to become an electrician either. It shouldn’t have to boil down to that binary choice.

Go and play with [the breadboard][bb].

—*Friday 9th April 2021.*

[rnt]: https://www.crossingtheruby.com/2021/04/06/hypertext-dsl-reading-nested-templates-from-file-2.html
[soc]: https://www.crossingtheruby.com/2021/02/23/sources-of-complexity.html
[sy]: https://www.crossingtheruby.com/2021/04/08/sew-with-hypertext.html
[bb]: https://www.crossingtheruby.com/2021/02/24/breadboard-vs-circuit-board.html
