---
layout: post
title: Framework Abstractions
---

> Frameworks have the potential to inhibit a deeper understanding of the things they abstract

—Jeremy Wagner, on [CSS Tricks][jwu].

Frameworks are, almost by definition, abstractions. Frameworks try to provide a convenient toolbox for abstracting away repetitive tasks and boilerplate that are common to the majority of projects in a given problem space. 

Frameworks are concerned with hiding away the lower level details to offer higher level primitives. Frameworks ‘wrap’ some form of complexity in a nicer package to make it easier to deal with. In some cases frameworks try to remove the supposed complexity out of the picture completely.

Take a back-end web framework as an example. The lower level details of HTTP are abstracted away and given a more accessible higher level interface with routers and controllers. The lower level details of SQL are abstracted away and given a more accessible higher level interface with an Object-Relational Mapper and native query methods. The lower level details of HTML are abstracted away and given a more accessible higher-level interface with form helpers, HTML helpers and sometimes even template engines. This is MVC; Model, View, Controller.

Even smaller aspects of the whole like the lower level details of session cookies are abstracted away and given a more accessible higher level interface with a native session object. The list could go on, there are abstractions at every level.

And this is unquestionably a good thing. It allows us to build a skyscraper from the 10th floor. It allows us to ignore the details until we’re ready to dive into them. As a programmer who cut his teeth with Ruby on Rails, I had no clue about HTTP, sessions, the inner workings of HTML forms, and only a cursory knowledge of SQL.

In the days before frameworks, back when we were bending blogging tools like WordPress into web applications, there was a lot to learn. I remember deciding that transforming something that was built for blogging into a foundation for web applications with radically different use-cases was not an approach I could get behind. The alternative was to purchase a stack of PHP books and learn the inner workings of HTTP and the Apache web server; how to deal with session cookies; how to connect to a database and execute raw SQL queries; How to hand-craft HTML. Long story short, I found Rails and ditched all the books. I could get started fast. I was amazed at what I could build.

On top of that, I didn’t have to understand the details. I trusted Rails to handle it all for me. And the trade-off was a good one. Of course, not having had any experience in any other language or framework save a smattering of PHP, I didn’t know any better. Rails helped me accomplish my goals.

As I matured as a programmer, I started to learn some of the lower-level details. This was necessary because a framework can’t hide away everything. And the more you use a framework, and the wider the range of problems you tackle, the more you need to get to know the lower level details. Think of SQL performance optimizations like avoiding n+1 queries.

The more I got acquainted with the underlying infrastructure, with HTTP, with SQL, with HTML, the more I thought two things: 

1. is the abstraction really _necessary_ and 
2. is _this_ abstraction the best possible abstraction

Some abstractions fit, and fit well. Others, having understood what they abstract, seem to offer more complexity in the abstraction than the things they abstract.

Of course, I never considered myself as the person who would right all the framework wrongs, indeed I didn’t think I was capable of it. But my discovery of Elm strengthened my conviction that there can be a better way. With Elm, and now we’re moving to the front-end, it represented such a stark improvement to the JavaScript framework equivalents that it felt like Elm was the little boy asking the crowd of JavaScript developers why the Emperor JavaScript frameworks had no clothes.

Elm abstracted the aspects of programming on the front-end in a tight and cohesive whole. Rendering, events, application state all bound together in the Elm Architecture. When you hear Evan Czaplicki speak, and see the deliberation of the Elm development process, then you understand how he sweats the details of abstractions, APIs and affordances. 

At ElmConf Europe 2017 there was a gathering of like-minded early adopters. And Elm attracted, if I may say so, those who shared Elm’s strong sense of _taste_. There I met a programmer called Michel Martens. He showed me a path to bringing Elm’s simplicity to the back-end with Ruby. What dumb-founded me was that he had been doing this since the start of Ruby’s rise, and contemporaneously with the rise of Ruby on Rails.

He, and a small group of partners in crime, had developed a set of beautiful, elegant and microscopic libraries which, when used together, were able to accomplish similar results as a large framework like Rails. That is, you could build a web application with a loosely coupled ‘framework’ of small, targeted tools, each of which provided a simple abstraction, or no abstraction at all, over the underlying web platform. Better than that, the lack of opinion allowed me, as someone who now knew what he was doing, to choose my own abstractions for particular problems rather than working with, and often around, the abstractions provided for me in a framework like Rails.

It’s not for the faint of heart.  Nevertheless I believe that learning the underlying infrastructure through the use of libraries that respect that infrastructure and teach you to build on those primitives rather than a library or framework’s own, new set of primitives is a much richer experience and makes you more resilient in the face of constant change.

I’d like to explore a few concrete examples of what I mean in future posts. 

Meanwhile, what is your experience of using frameworks? Do you sometimes feel like you are working against the framework? Have you ever felt cheated by the framework because it hides something from you that you would have preferred to be aware of? Are you interested in learning lower level details or are you happy inhabiting the higher levels? Let me know. 

—*Monday 8th February 2021.*

[jwu]: https://css-tricks.com/radeventlistener-a-tale-of-client-side-framework-performance/
