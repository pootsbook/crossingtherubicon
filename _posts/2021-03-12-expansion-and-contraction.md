---
layout: post
title: Expansion and Contraction
---

When building out a small HTML helper library I experienced a pattern that I see in a lot of software, product and even business development.

I call this pattern _expansion and contraction_. It operates on a number of levels, so let’s start with the concrete instance of my attempts at a solution to the HTML helper problem in [post three][hp3] (expansion) and [post four][hp4] (contraction) and then [post five][hp5] (further contraction).

In many ways expansion and contraction is analogous to divergent (expansion) and convergent (contraction) thinking. That is, when exploring a problem space you are entering an unknown of sorts, and like a gas expanding to fill the space available to it, you scratch around in the darkness trying to give form to something that is unseen. This is expansion, divergence, broad, all-encompassing.

When enough expansion has been done, when divergence has reached its goal, when a form has been found, when you can see, then this is the point at which to contract, to converge, to go narrow, to encompass only that which is truly necessary.

You know this to be the case. Think of a traditional code spike. You are departing for a moment from the structured approach you might normally employ for building quality software. In XP, this is the moment you abandon tests because you don’t know what it is that you should test. You diverge, you expand, you through some spaghetti against the tiles on the wall to see what sticks.

In a successful spike—when following all of the rules—you throw away your code, or you file it neatly away, and you start afresh, this time test-driving your way to the known solution. You are contracting, you are converging, you are happy that the spaghetti is cooked just enough to be able to serve it on a plate.

In this process of contraction, you are in a sense also working on the bolognese sauce. You are simmering away the excess to leave a concentrated fusion of ingredients with maximum taste. You distil the code by taking away that which is unnecessary.

You know this to be the case. Think of test driven development. Red-Green-Refactor. You present yourself the problem in the form of a failing test. Then you make the test pass. And then, if you are doing things by [the book][tdd], you _refactor_. Getting the test to pass is expansion. Refactoring the code to make it just right is contraction.

Think of product development for a product-led company like [ClubCollect][cc], where I work. To a certain extent you don’t know whether or not a given feature or set of features will strike a chord with your user-base. You have your gut and intuition, you have user research, you have the constant feedback from a stellar support team, but there are myriad ways to solve any given problem. 

You have to expand, to deploy a broad solution or set of solutions, all the while observing, communicating, learning. And then when you do hit the right note it’s time to contract. To focus your energy and attention at the sliver of value that has been provided and throw away the detritus that has now served its purpose in bringing you to this insight.

In business development it is the same. Your market research has to be broad, has to be open-ended, has to be expansive, has to enter an unknown. When you start hearing the same problems coming up time and again, you know it is time to contract, to zero in on a particular area, particular market, particular group and concentrate your energy and attention there.

And it works also at a system level, thinking of web applications. There is a period of expansion when features are built as demand comes. These features often don’t have any relationship to each other, or maybe they do, but that relationship could not have been known beforehand and thus are disconnected. And this is where a lot of software companies make a wrong turn. Or rather don’t turn at all. They plough on. 

I submit that it pays to take stock, to observe the divergent architecture of features that you have created and then to consolidate, to contract. To refactor the underlying code to reflect your new understanding of the problem space. Rewrite.

Perhaps I’ll go into some concrete examples of this in future posts.

—*Friday 12th March 2021.*

[hp3]: https://www.crossingtheruby.com/2021/03/08/a-small-html-helper-library-3.html
[hp4]: https://www.crossingtheruby.com/2021/03/09/a-small-html-helper-library-4.html
[hp5]: https://www.crossingtheruby.com/2021/03/10/a-small-html-helper-library-5.html
[cc]: https://www.clubcollect.com/en/
[tdd]: https://www.pearson.com/us/higher-education/program/Beck-Test-Driven-Development-By-Example/PGM206172.html
