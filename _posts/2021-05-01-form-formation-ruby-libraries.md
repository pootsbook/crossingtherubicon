---
layout: post
title: "Form Formation: Ruby Libraries"
---

There are several Ruby web frameworks that can be used as alternatives to Rails: [Roda][roda], [Hanami][han], and [Trailblazer][tb]. Trailblazer started off as an independent framework but has since become a framework-agnostic layer for business logic (including forms). 

Of course the “anti-framework” that has featured on the pages of this blog which is made up of a loose collection of (micro-) libraries also has a ‘form object’ library, Scrivener.

## Alternative Frameworks

Roda comes with [forme][forme] (and [autoforme][aforme]). It is designed to make creating HTML forms easier, with flexibility and simplicity as primary objectives.

Hanami comes with its own form helpers, [Hanami::Helpers::FormHelper][hhfh] ([guide][hg]), similar to how Rails bakes this into the framework. It focuses on HTML generation. It also has a [Validations][hv] mixin which can be mixed into a form object providing similar functionality to the next two items in this list. 

Trailblazer has [reform][reform] ([docs][rfd]) which leaves the HTML generation up to you and instead allows you to create a framework-agnostic form object complete with validations.

[Scrivener][sv] is a small, stand-alone library for validating forms and falls into the form object category, similar to Trailblazer. It too is framework-agnostic.

At this stage it’s clear that the libraries divide mostly into two clear categories. One for a form’s HTML generation and another for a form object which often includes some sort of validation strategy.

There’s a lot of material to examine and I hope to do that in future posts.

—*Saturday 1st May 2021.*

[roda]: http://roda.jeremyevans.net/index.html
[han]: https://hanamirb.org
[tb]: https://2019.trailblazer.to/2.1/
[forme]: https://github.com/jeremyevans/forme
[aforme]: https://github.com/jeremyevans/autoforme
[reform]: https://github.com/trailblazer/reform
[rfd]: https://2019.trailblazer.to/2.1/docs/reform.html
[sv]: https://github.com/soveran/scrivener
[hhfh]: https://www.rubydoc.info/gems/hanami-helpers/Hanami/Helpers/FormHelper
[hg]: https://guides.hanamirb.org/helpers/forms/
[hv]: https://guides.hanamirb.org/validations/overview/
