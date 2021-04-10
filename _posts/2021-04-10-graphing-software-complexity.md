---
layout: post
title: Graphing Product Complexity
---

Yesterday I considered how complexity is increased when a software library tries to be [all things to all men][atm]. 

As is so often the case the principle seems to apply not just to the code behind the software but the software, or product, itself.

As a rule of thumb I often chart product complexity as belonging to one of two graphs: a linear graph and an exponential graph.

The y-axis is the degree of overall product complexity from low to high and the x-axis is time from some time in the past or present until the future. The graph charts the effect of a given feature, if developed and integrated into the software, to the overall complexity of maintaining or using the product over time.

And in my head, it is a graph per feature, and a feature then manifests itself as either linear or exponential. It’s not an exact science, but more a heuristic, a tool for thinking and analysing in short shrift.

And often the analysis boils down to the orthogonality of a feature. Now, orthogonal is one of those words that people wheel out to sound smart and discerning, and often as a trump card to try and end an argument when on the losing side. And it is used then in the sense of “immaterial to the current debate”. 

I am using it in the mathematical sense of perpendicularity. Two features are orthogonal if they are perpendicular to each other, that is, they do not touch and operate along different axes of the software. Now in software not much is at a right angle so I’d perhaps go further and say the angle doesn’t matter too much as long as the lines do not intersect.

In even more granular and everyday terms we could say that the code required to create and maintain two features does not overlap (or, but this is getting really pedantic, their overlap bears no influence to the peculiarity of each feature, and thus moot).

A basic example of this from a platform I have had the good pleasure of not using for a number of years is the authoring of a Tweet and the editing of one’s Twitter profile. With the exception of a Tweet having reference to the user who wrote it, writing a tweet and modifying your profile description are two different parts of the application likely working on different database tables and different sections of the codebase. They have little to no connection to each other, and the code for one will be independent from the code for the other. The product also retains this independence in its form and usage. 

This then is an orthogonal feature which manifests linear complexity. The additional complexity to the whole, which comes from adding this feature in part, is straightforward, predictable and relatively benign. 

Note that this sum is independent from the complexity of the feature in and of itself; we might be adding a complex feature, but that complexity does not affect the whole in making the whole more complex in a manner disproportionate to the addition of this part.

A feature which is intersectional, is one with cross-cutting concerns. The concept and the code applies across a range of other features. For a feature of this kind the best case scenario is the smallest possible surface, which, to be distinguished from an orthogonal feature, must then by definition have a range greater than its own surface. We can think of the tremors of an earthquake moving out from the epicentre, or the ripples of water after throwing a stone into a calm lake or pond.

On the extreme end an intersectional feature is one that intersects with all other features. The archetypal example is authorization. You can even make an argument that, depending on the requirements, authorization can even be considered as an accelerant to exponentiality.

With authorization the assumption is that each action taken within the product, and thus each code path attached to an action must be protected with code that authorizes that action on the basis of the user that is authenticated. Now this acts as intersectional on two counts.

First of all, each code path that already exists must be modified to respect the authorization rules. Secondly, each _future_ code path will have to take the authorization rules into account and apply them in their particular circumstances.

So every feature discussion after the addition of authorization must now entertain a distinct discussion point about who may or may not have access to this particular feature.

(The reason I said it could be an accelerant to complexity is that there are many flavours of authorization. The simplest, role based authentication, is vastly more straightforward than user based authentication, that is, where each individual user has a different set of permissions. I need only to mention Amazon Web Services (AWS) Identity Access Management (IAM) to a roomful of cloud consultants to reduce them to quivering wrecks.

And as if that weren’t enough, you now have to develop a consistent user interface to cater for all of the different views that may appear based on whether or not certain buttons, actions, data etc. is or is not available.)

This is why, when asked about these kinds of features, they receive a firm and default, “No”. Or to assuage the more persistent, “Phase two”. This is the kind of complexity you introduce after Series A when there is money in the bank. I mean, the best time to think about it was probably forty years ago, at the start, but the next best time is not now, it is some time far in the future.

So this intersection is not only about the here and now, but about, now, _and_ in the future. Another classic case is auditing. If you have to have an audit log for some or all of the activity carried out in your product, then this also reaches its hand into almost every area of the code. Especially if you are doing it after the fact. The kinds of things that would have been better off thinking about from the start, but when you start out, you don’t even know if you have a commercially viable business so there’s no point in investing in something that may not turn out to be a necessity.

So I put most features into one of two buckets. The second bucket needs to be triaged on the basis of attack surface. It’s not coincidental that I use a term normally reserved for information security as a means of defending simplicity in software and product.

How do you think of complexity in both software and product? Do you agree with my rough categorization? Can you think of further examples along these lines? What are the heuristics that you use to classify different levels of complexity?

—*Saturday 10th April 2021.*

[atm]: https://www.crossingtheruby.com/2021/04/09/all-things-to-all-men.html
