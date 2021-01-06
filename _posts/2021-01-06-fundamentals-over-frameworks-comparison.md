---
layout: post
title: "Fundamentals over Frameworks: Comparison"
---

The last few posts have been terribly abstract. In [Fundamentals over Frameworks][fof] I proposed that the knowledge you aquire when learning a framework is often non-transferrable, whereas the knowledge acquired when learning a language is often more generally applicable.

If I take Ember.js as an example of a framework, and Elm a language, then I can probably more concretely illustrate precisely what I mean. Of course all the normal caveats apply: this is a generalisation; there are things in Ember.js that will be generally applicable; there are things in Elm that will be non-transferrable; Ember.js is consciously ambitious, Elm is consciously minimalist, et cetera. 

One of the reasons I find this particular comparison interesting is that they are both operating in the same space. They both strive to offer a compelling solution to what is generally termed a Single Page [Web] Application (SPA). Leaving aside this misnomer for frameworks that allow you to achieve precisely the opposite, Ember and Elm represent two radically different approaches to the same problem.

Now when I picked up Ember.js it was in the early days of the framework, and some of my grievances may of course no longer apply. And lest I should be misunderstood, let me clearly state that, in the right hands, Ember.js offers serious productivity gains. Nothing I say here should be taken as a slight. If I knew other frameworks as intimately as I knew Ember I could probably use those as examples instead.

Learning the framework early in its development came with a certain number of drawbacks. One is that the Ember release process is one of the most impressive in open source software. Yes, you read that right, the speed at which they update the framework presents challenges in staying on top of it. And this is illustrative of the point. If something is often changing in the framework _itself_, it is a sign that the knowledge you picked up, that has been outdated by a new version of the framework, is not even transferrable to the newly released version of the framework let alone the world outside of the framework.

Now one of the reasons I think that frameworks can be helpful is that they give you a set of well-defined buckets into which you may organise your code. The wiring of all of these buckets is the framework’s responsibility with the idea being that the framework saves you from a lot of soldering. A motherboard of sorts into which you plug your own components using the interfaces that the framework provides. So in Ember.js (version ~1.10) you had the concept of a Route, which routes to a Controller, which controls a Service, which services a Model, which models your data. The interconnections remained relatively opaque but as long as you knew what to put where it all just worked.

But all of these concepts and their implementation are unique to this particular framework, despite any surface level resemblance to other frameworks. The terms might be the same, but they bear as much similarity to each other as Vim the cleaning product and Vim the text editor. If I stop using Ember, I can start cleaning out my mental buffer in a jiffy.

Elm approached the problem from a language level. There are few unique concepts in Elm that are not generally applicable. The wiring of the framework? The `update` function. A function. A function with arguments and a return value. The same as a function in any other language. The application’s state? A record. An object. A dictionary with keys and values. The same as the equivalent data structure in any other language. The programming model? A reducer, operating on the application’s state, and returning a new state. Just like a reducer in any other language. 

Sure, the wiring is not hidden behind the desk, but it’s all there, in front of you. Explicit. And you can reason about it using the tools you already know. Modules and functions. That’s it. Are some things awkward? Yes. Is there boilerplate? Yes. Do I have a feeling of confidence and freedom because I know what’s going on? Yes.

But more importantly for this post, what I learned by learning Elm is applicable anywhere else I go. In every framework, in every language. 

Elm is decidedly evergreen.

—*Wednesday 6th January 2021.*

[fof]: https://www.crossingtheruby.com/2021/01/02/fundamentals-over-frameworks.html
