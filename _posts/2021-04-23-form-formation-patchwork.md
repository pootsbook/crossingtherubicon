---
layout: post
title: "Form Formation: Patchwork"
---

At this point I have looked at all four of the [constituent parts][fcp] that I identified are necessary in form generation:

1. HTML and
2. [Styling][fs]
3. [Model Object][mo]
4. [Life Cycle Meta-Data][lcmd]

I am trying to understand where precisely the complexity lies before attempting to create a way of putting forms together in a declarative fashion.

The conclusion I came to is that the complexity does not necessarily lie in any of the individual parts listed above, but on their combination.

One of the ways in which I’d like to explore the problem space is to investigate several different approaches to form building in Ruby and potentially in other languages and frameworks as well.

By examining this from different angles I hope to be able to spot similarities and differences in approaches and to categorise solutions along broad lines. Along the way I hope to figure out why certain libraries have made certain trade-offs and to begin to get a sense of what an optimal—in my eyes—approach might be.

I think it’s also worth stopping at this point to consider why precisely a declarative approach is so attractive, and to take stock of some observations at this early point in the process.

In future posts, before surveying form libraries, I want to explore: forms as a mini-case of the Model View Controller architecture; form fields and controls as a function of application state; the inversion of control that a declarative approach brings compared with the usual imperative style; and the advantages that a declarative approach might bring.

—*Friday 23rd April 2021.*

[fcp]: https://www.crossingtheruby.com/2021/04/19/form-formation-constituent-parts.html
[fs]: https://www.crossingtheruby.com/2021/04/20/form-formation-html-form-styling.html
[mo]: https://www.crossingtheruby.com/2021/04/21/form-formation-model-object.html
[lcmd]: https://www.crossingtheruby.com/2021/04/22/form-formation-life-cycle.html
