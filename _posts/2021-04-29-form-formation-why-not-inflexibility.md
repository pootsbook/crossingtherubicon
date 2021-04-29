---
layout: post
title: "Form Formation: Why not? Inflexibility"
---

Yesterday I made an argument for declarative forms on the basis of [testability][ffw]. The key to arriving at this benefit is a certain rigidity and inflexibility.

At this stage, this seems to be the strongest argument against defining forms declaratively. As we shall see in future, when we take a look at different form libraries and frameworks, most of these solutions are imperative.

I believe the reason most of the approaches are imperative is because this is the easiest way to deal with change. And change is the only constant in application development. Not only change in terms of one form changing over time, but also change in the sense of one form differing (or _changing_) from another.

Take scaffolding as an example in both Rails and Phoenix frameworks. These are both code generation tools. To generate the scaffolding code you define a resource in a declarative fashion, but the code that is generated you subsequently handle in an imperative fashion. Why is that?

The main reason is that scaffolding is exactly that, scaffolding. Intended as a light structure from which you can build the real thing. Once the real thing has been built the scaffold ceases to have any value. This assumption, that changing the code that the scaffold generates is necessary, is baked in from the off.

Otherwise there would be no need for code generation. If you could define everything declaratively, you would. There would be no need to see the code that gets generated and to put it into a file, and then to commit it to source control. There would be no need to touch it. To modify it.

Forms then are in this sense microcosms of application level concerns. A given form will have a unique requirement, a different layout, a modified form control. And if we are to bake these unique edge-cases into a declarative framework then the complexity of the framework far outweighs the complexity of writing the HTML from scratch each time. Especially if you have helpers to, well, help you.

There are interesting paths to investigate at the extremes of what is possible declaratively. I touched on visualizing a form’s HTML markup structure to compare different forms. What if there was a way to  manipulate this graph in AST form as a means of reducing the difficulty of catering to edge-cases in a declarative approach?

That feels too much like detonating an atom bomb to ward off a fly. A much simpler and saner approach would  be to simply limit the edge-cases, to settle on a convention of less is more. To take the hit, lose the flexibility, and gain the uniformity that allows a declarative approach. Perhaps that’s a pipe-dream.

Is that even possible? Is it practical? I don’t know, but I’m interested to find out.

—*Thursday 29th April 2021.*

[ffw]: https://www.crossingtheruby.com/2021/04/28/form-formation-why-testing.html
