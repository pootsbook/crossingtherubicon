---
layout: post
title: "Elm: Language or Framework?"
---

<small>
  <em>
    Penned on 17th January 2016
  </em>
</small>

Elm sits apart from the curent crop of frontend tools and frameworks in more ways than one, but what I want to explore is that it is not so easily classified. I will argue that Elm is a mixture of both language and framework, or even of all three dominant classifications: library, framework, language.

The most obvious is, of course, that Elm is a _language_. It has its own syntax and semantics, different to that of the host platform, JavaScript. This is a double blessing: it saves us from JavaScript, giving us a more terse, expressive way to write our applications; and it is foreign enough that it will deter those looking for a quick fix.

Elm is a _framework_. The language is such that it forces you to build applications in a certain way, constraining you, positively, towards a pattern that will make the construction of your application better than if you hadn’t had the pattern in the first place. Frameworks often codify idiom in this way, elevating our ability to reason about an application to the correct abstration level by providing a set of common primitives that help us escape the low level. 

The way in which Elm does this is the Elm architecture of Model (single state atom), Update (definitive set of actions), and View (stateless rendering). What I find compelling is that Elm accomplishes framework level idiom through language-level semantics. Perhaps a clearer way of phrasing that is that Elm can express the concepts a framework needs (model, update, view, action) without building another layer on top of the language, adorning it with model or component classes. 

The language contains everything that is required to operate at framework level through the features of immutability, the type system, with union types. (Compare Redux actions with Elm actions.) This is why I think it is possible to also put Elm in the category of framework. The package manager then becomes equivalent to a framework’s add-on system, no bells or whistles required.

The last classficiation, _library_, is a little more tenuous, but hear me out. You can treat Elm as an external dependency in your React/Angular/Ember/vanilla (delete as appropriate) application and, using ports, siphon off any particularly taxing problem for which the Elm language might be better suited. You don’t need to use it for rendereing, or for structuring your existing application but you can easily reap the benefits of Elm.

As you can see, Elm really does blur the boundaries between library and framework to the point where the Elm language is so expressive you are left wondering why you would need a framework. The Elm architecture, type system and package manager makes a lot of what a framework exists for redundant. There would be a few unresolved questions, like how best to standardize on patterns for applications as they get larger in terms of code organization, but these are trivial matters in light of what has already been done for you.

—*Tuesday 12th January 2021.*
