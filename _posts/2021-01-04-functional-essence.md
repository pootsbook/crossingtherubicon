---
layout: post
title: Functional Essence
---

The difference between essence and accident can be seen in the [migration to clean architecture][aa]. I believe that we can also make the case that functional programming focuses more on essence while object oriented programming on accident.

Although there are a wide variety of definitions for what makes programming functional, I want to focus on the following key characteristics which are all interlinked: declarative code vs. imperative code; the separation of data and behaviour (i.e., data structures and functions operating on those data structures instead of objects with data and methods); immutability or statelessness (i.e., the transformation of data structures from one (immutable) form to another vs. the in-place editing of an object’s (mutable) state); and lastly pure/impure separation (i.e., the explicit division of functions that are stateless (pure) and those that have side-effects (impure) vs. no clear division between a method that, for example, queries or commands an object).

Declarative code can loosely be defined as code that describes _what_ should be done, whereas imperative code explains _how_ it should be done. Imperative code is concerned with explaining to the computer the steps that must be taken (accident) while declarative code shows the desired outcome (essence) and is not concerned with the lower-level details of how to do it.

The separation of data and behaviour, with the incumbent focus on data structures and their transformation via functions, also allows us to focus on the essence of a problem, approaching it at a higher level. We are not then concerned with the accident of prying into an object’s internal state or debugging the interplay of interconnected objects. The data and the transformations are front and centre.

Immutability removes the ability to change a data structure, instead preferring to produce an updated structure as a separate entity. While this seems like an unwelcome constraint it breeds a level of confidence, at the application level and/or when working in large teams, that nothing or no-one can have caused a subtle bug by changing your state mid-flight that could potentially lead to undesirable outcomes. Again, you are free to focus on essence without the accident (waiting to happen) of who changed what, when. 

Pure-impure separation follows on from immutability. By clearly designing functions and even whole sections of code to be free from side-effects and dividing them from areas that do have side-effects you are able to operate at a higher level of granularity. This can be contrasted with a blurring of these boundaries and the ensuing tar pit. While in this post I am focusing on the adoption of the designation versus the actual separation between the two elements, this separation can itself be seen as a means of dividing essence from accident. 

Each of these chosen characteristics of functional programming demonstrate a level of improvement over object oriented programming along this axis of essence and accident. 

—*Monday 4th January 2021.*

[aa]: https://www.crossingtheruby.com/2021/01/03/accidental-architecture.html
