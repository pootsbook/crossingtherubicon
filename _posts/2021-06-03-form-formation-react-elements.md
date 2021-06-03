---
layout: post
title: "Form Formation: React Elements"
---

This post has very little to do with form formation but arises from my observations about HTML generation in form libraries [yesterday][dx] as well as earlier [reflections][ref].

Some libraries have an ad-hoc implementation of an HTML library included within them. This is not really a surprise given the tight pairing of a form’s representation with its HTML. It is a problem that needs to be solved one way or the other.

It made me think of React Elements. React has an advantage that you can define your markup (and component) tree as a simple JavaScript object, like an abstract syntax tree. I believe this can be used interchangeably with straight HTML or JSX for both traditional HTML elements and custom React components.

In React there is then little distinction between this ‘AST’ as expressed in a JavaScript object and the components and HTML themselves. It would be possible to mix and match widgets (components) inside a greater declarative structure. This would avoid the need for custom templates or rendering.

In some ways my experiments that led to something very like [Hypertext][ht] were me working backwards to this state of affairs in Ruby. Although there is no explicit AST, we can define markup with Ruby methods, making the inclusion of particular logic, the setting of attributes or even the interpolation of variables something possible in the language proper.

So it seems inevitable that trying to write a form library would ultimately lead to an HTML library to support it. I must have subconsciously cottoned onto this given I started with the HTML library.

—*Thursday 3rd June 2021.*

[dx]: https://www.crossingtheruby.com/2021/06/02/form-formation-developer-experience.html
[ref]: https://www.crossingtheruby.com/2021/06/01/form-formation-reflections.html
[ht]: https://www.crossingtheruby.com/2021/03/14/hypertext-write-html-in-ruby.html
