---
title: HTML Gardening with Herb
layout: post
---

[Marco Roth][1] released a new HTML-aware ERB parser library called [Herb][2] at the Ruby Kaigi conference a few days ago. Given I’ve been playing around with [Phlex][3] over the last week and toying around with the idea of automating templating concerns in a conventional “views and partials” Rails application, it’s perfect timing.

My preference for eschewing a templating language in favour of using the host language natively, inspired by Elm, led to explorations a few years ago which resulted in Michel Martens’ [Hypertext][4] library. In the intervening years Joel Drapper has seen some traction with Phlex, conceived with the same ideals, but offering a much more robust and fully-featured solution which plays nicely with Rails.

A key practical benefit of Phlex (widely misunderstood) is that it is a faithful abstraction of HTML (i.e., it doesn’t leak), offering a degree of correctness that a string based templating language like ERB can never provide. If the Ruby can’t interpret a Phlex “template” file (remember, it’s just Ruby) then there is a problem with your “HTML”. If Ruby interprets it, then it is valid HTML. In ERB, you can type anything you want, and you give yourself over to the grace and leniency of browser error recovery when it is presented with malformed HTML. [Strings are a leaky abstraction for HTML][5]

In one sense Herb closes this loop. Given it parses ERB files it is able to highlight malformed ERB. So adding it as a linter might give you a certain level of safety. I ran the parser on ClubCollect’s ERB files and in just under twelve-hundred files it found seventy-five parse errors. That’s a defect rate of six percent. Granted, it’s not the end of the world—they are probably all missing tags or malformed HTML, quirks that a browser will forgive—but I’d feel much more comfortable knowing that I couldn’t make those mistakes in the first place.

Herb parses ERB files into an abstract syntax tree which models both the HTML and Ruby parts of an ERB template. The primary purpose of the library is to improve language and editor tooling when working with ERB. What I find more interesting is: firstly, being able to more accurately query the ERB than a simple text-based search; and secondly, being able to modify and/or transform template files programatically.

## Querying ERB Semantically

ClubCollect’s codebase is over eleven years old which in modern terms is like ancient history. The accretion of different layers of symbolic sediment, templates subjected to the whimiscal vagaries of different styles and preferences, is something crying out for careful archeological excavation followed by judicious rehabilitation. To give a small, but practical example of stemming the ruinous tide of divergent directives, consider all of the ways that one might have declared a simple Bootstrap Alert component: plain HTML; interpolated Ruby in the HTML `class` attribute; via a partial; via a helper method. In the first case, simply searching for the string `"alert"` might yield what I’m looking for, but might also return a number of false positives.

```
<div class="alert alert-primary" role="alert">
  A simple primary alert—check it out!
</div>
```
(Bootstrap 4.5.6, this is archeology after all)

With Herb, it is possible to parse the ERB, and because it models the HTML in an AST, it is possible to search for the value `"alert"` present specifically inside an HTML `class` attribute. This may not return all the cases, but it reduces the set of false positives.

## Modifying ERB Programatically

Let’s say that we want to coalesce our different compositions of Bootstrap Alert into some form of component. Maybe history hindered a former colleagues adherence to accessibility (maybe that former colleague was me!) and left out the `role` attribute. Having located instances of the Bootstrap Alert it should be possible, with the Herb AST, to ensure conformance to the proper HTML definition, and if it falls short, supplement the existing definition with the missing piece(s).

Looking further, but remaining in ERB, we could write a helper method and ensure that all instances of the Bootstrap Alert use the helper method, rewriting the AST, or simply re-rendering the ERB to file with this modification.

And should one wish to make the switch to a more component-based view layer with GitHub’s ViewComponent or (should I win my colleagues over) Phlex, it should also be possible to automate the transition from ERB.

These two Herb use-cases are things I would very much like to explore.

—*1819, Saturday 19th April 2025.*

[1]: https://marcoroth.dev
[2]: https://herb-tools.dev
[3]: https://www.phlex.fun
[4]: https://github.com/soveran/hypertext
[5]: https://joel.drapper.me/leaky-strings/
