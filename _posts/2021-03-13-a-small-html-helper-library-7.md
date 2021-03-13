---
layout: post
title: "A Small HTML Helper Library (7)"
---

Previously I wrote about [expansion and contraction][eac] in the general sense. I drew analogues from Extreme Programming (XP), Test Driven Development (TDD), product-led growth and its implications on application architecture among other examples.

In this post I’d like to zoom in on the expansion and contraction specific to the process of arriving at my final (-ish, is software ever final?) solution to the problem that my small HTML library was trying to solve.

In addition, I want to focus very specifically on why contraction is so valuable and how it worked itself out in this particular case. And that reasoning about software architecture and design from first principles yields a simplicity that commonly escapes us in the day-to-day.

I am very grateful to [Erik van Eykelen][eve], my predecessor as CTO of ClubCollect, for granting me, for the very first time in my professional career, the time and space to truly exercise my convictions relating to profound simplicity in software design. In future posts I’d like to touch on several refactors in the ClubCollect Rails monolith that I undertook in this line.

If I were to sketch out the development of my thought throughout the whole process of writing the HTML library it would look something like this:

1. Attempt to write a Ruby library for generating HTML inspired by Elm’s HTML library.
2. Succeed at writing an Elm-ish HTML library in Ruby.
3. Attempt to Ruby-ify the API, syntax and code.
4. Use the Elm API as a template for what should be created under the surface of the Ruby syntax (a simple abstract syntax tree (AST) of sorts).
5. Struggle to figure out how to generate the AST with Ruby blocks.
6. Figure out a way to use blocks but fail to generate a tree structure.
7. Generate a flat structure which encodes the tree and transform the tree to an AST and then render.
8. Feel a dissatisfaction at the design of the code and sleep on it.
9. Go back to the drawing board, think about the problem, realise that there is no need to generate an AST when you can simply generate the HTML directly.
10. Delete all of the AST code and replace it with the HTML rendering code.
11. Make some small refinements.
12. Done.

Steps 1–7 are all expansion. Steps 8 and 9 are the turning point. Steps 10 and 11 are contraction.

So we can see the pattern of expansion and contraction in this exercise more clearly now. You can read the posts to get a sense of the mechanics of what happened.

What I’d like to explore are steps 8 and 9. These are the steps that are often omitted in the conveyor belt of the feature factory. They are not necessarily forgotten, but there is no remit to refactor within the organization. Viewed through dim glasses refactoring has no business value. If the true definition of refactoring is adhered to, that is, changing the code without modifying the behaviour, why would you pay someone money to achieve nothing visible to the end-user? Short-sighted of course, but one can sympathise when stated this plainly.

You’ll notice that during this period there are a few things that I think form part of the necessary set of behaviours required to move towards simplicity.

The first is _dissatisfaction_. A feeling of incompleteness. Perhaps an ideal. A sense of craftsmanship, of workmanship, of beauty, of taste. I think it’s fair to say that not everyone possesses this, or if they do, it’s been buried out of sight. There are some who shrug their shoulders, “it works”, and there are others who find it hard to let go of a job they feel is not well done. Of course there are contexts that influence one in one direction or the other, pragmatism can be friend or foe.

The second, in my specific circumstance, was sleep, but I could generalise to _time_. Time is an underrated and underappreciated ingredient. Blame our culture of now. Sometimes you have to let the pot simmer to bring the flavour out. You can’t turn the oven up to twice the heat and bake a cake in half the time. Your brain needs to work on the problem in the background. I think this is what Rich Hickey calls “Hammock Driven Development”, what Mr. van Eykelen calls “noodling on it”. Allowing the subconscious mind to get to work in the background. Allowing the conscious mind to consider all the angles in the foreground.

The third is not explicit in my list, but implicit: you need a _deep engagement_ with the problem space. A superficial understanding will not do. The deeper you get into a problem, the clearer the options will appear. (Does this work by process of elimination? As in, the deeper you go the more constraints you uncover so that your solution is nothing other than the only solution that remains?)

And the fourth is revisiting the problem from _first principles_. That is, on your way to a final solution to the original problem you are constantly solving intermediate problems. Sometimes these problems are of your own creation. That is, the path that you choose to walk towards a solution brings problems that are unique to that particular path, but not necessarily in any way essential to the solving of the original problem.

You can clearly see this in my journey. Given I started with Elm-ish HTML, and then made the jump to Ruby while deciding to use the format of the Elm-ish methods and arguments as an AST of sorts I actually created problems for myself. I solved those problems, generated a tree, did so in a roundabout way and arrived at the final HTML. But that AST problem was not essential, no. The essential problem (in my case) was figuring out the nesting issue with ‘grandparents’ and sibling block output. Once that was solved it worked for both the AST interim solution _and_ for the final solution. 

And the final solution came about by reassessing what was precisely necessary and removing what was not. Going back to first principles: the generation of HTML with a Ruby DSL. 

A potential fifth is _refinement_. This is what I would call the long-tail. Most of the value has been arrived at during the period of contraction. And like a logarithmic curve tending towards the asymptote, refinement is putting the cherry on top. Not essential, but adds a certain _je ne sais quoi_. It distinguishes.

I think there are other factors in addition to these five. I’d be interested to hear if you have any thoughts on what they might be. For example, mastery of your tools is one that pops into my head in answer to my own question.

Do you recognize this process of expansion and contraction in your own work? Do you have the available time and attention to devote to arriving at a potential breakthrough? Or are you happy to move on, job done, next…

—*Saturday 13th March 2021.*

[eac]: https://www.crossingtheruby.com/2021/03/12/expansion-and-contraction.html
[eve]: https://bitgain.com
