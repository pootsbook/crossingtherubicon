---
layout: post
title: Rails Routing Code Complexity
---

Previously I examined some of the trade-offs in [Rails’ and Syro’s approach to routing][rsc]. I left one trade-off out which I would like to consider in this post. First, another look at trade-offs.

Weighing trade-offs only really makes sense in the context of a particular balance or set of scales; that is, given properties of two different approaches could be seen as both good and bad depending on the particular perspective from which it is viewed. This is influenced heavily by what goals you have or even by your personal preferences.

For example, someone might find the construction of URL paths by hand tedious and repetitive and therefore opt to use URL helpers as a convenience and short-cut. Someone else might like the transparency of the connection between Syro’s tree oriented routing model and thus forgo URL helpers.

The following trade-off is also subject to your perspective and goals. The reason I mentioned that it was lying under the surface is because it relates to the code behind or under the API or DSL that governs routing and helpers. For some, the internals are a black box and they couldn’t care less how the DSL is put together, as long as it works. For others—and I now fall firmly into this camp—they like to know what is going on under the bonnet, how the engine works.

I already compared one aspect of Rails routing DSL with Syro with respect to significant lines of code (SLOC) at the end of my [routing comparison][rc]. If we turn our focus to the URL helper code then we venture down a deep rabbit hole. 

As Giles Bowkett points out so eloquently in his classic “Rails As She Is Spoke”, writing about `url_for`:

> I’m not really sure how this code works at all.
>
> —Giles Bowkett, _Rails As She Is Spoke: How Rails Breaks Object-Oriented Theory, And Why It Works Anyway_ (Self-Published, 2012), 20.

He’s pointing to five _different_ definitions of the `url_for` method in Rails 3.2, and they’re still around in Rails 6. Now, while Mr. Bowkett could be accused of bombast there is often more than a grain of truth in his analyses. In this case, in all fairness to Rails, we can whittle down five definitions to two main methods:

- `ActionView::RoutingUrlFor#url_for`  ([code][uf1], 724 SLOC)
- `ActionDispatch::Http::URL#url_for` ([code][uf2], 303 SLOC)

Again, the code for both (including supporting methods) comes in at more than double the complete Syro codebase (indeed codebase is even a misnomer here, Syro is one file). And that’s just `url_for`, not including all of the code that is distributed elsewhere to make use of these methods, and discounting all of the route mapping code which makes `url_for` possible.

“It works” and “it’s convenient” are both acceptable statements, but at what cost? I don’t believe I’m the only one that has found themselves wading through this mud of complexity to figure out why a particular incantation of `url_for` or other route helper is not producing the desired result. I never even considered it in the past, but consider this: in the time it would take to read through the documentation and examine the code I could have written out the desired URL about a hundred times and have total confidence that it would work.

Trade-offs have costs. And some things we take blindly perhaps not knowing any better. Perhaps we are subconsciously subject to a form of cargo-culting, going with the flow, doing what everyone else does. And we never stop to think, what is this trade-off bringing me? And what is it costing me?

In this particular case I believe a decision for a little more inconvenience in the short-term leads to a significant reduction of inconvenience in the long term. A stitch in time saves nine.

—*Wednesday 16th February 2021.*

[rsc]: https://www.crossingtheruby.com/2021/02/15/syro-rails-routing-tradeoffs.html
[rc]: https://www.crossingtheruby.com/2021/02/13/framework-abstractions-http-rails-vs-syro.html
[uf1]: https://github.com/rails/rails/blob/291a3d2ef29a3842d1156ada7526f4ee60dd2b59/actionview/lib/action_view/routing_url_for.rb#L79-L119
[uf2]: https://github.com/rails/rails/blob/291a3d2ef29a3842d1156ada7526f4ee60dd2b59/actionpack/lib/action_dispatch/http/url.rb#L50-L56
