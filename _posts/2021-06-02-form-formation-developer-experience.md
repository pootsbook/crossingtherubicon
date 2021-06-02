---
layout: post
title: "Form Formations: Developer Experience"
---

While talking to a reader of the blog about my research so far, he mentioned a pet peeve when working with form libraries.

A common approach when debugging something is to grep (search) for the HTML fragment that you can see in the browser’s web inspector. The trouble is, with most form libraries, you won’t find an equivalent string anywhere in your code base.

Each form library, those that generate the HTML, has a different way of putting the HTML together. In Rails it builds on the base `content_tag` helper method which offers a way of generating HTML within Ruby. In other libraries they have their own ways of generating HTML. In fact, two of the libraries that I have yet to write about look like they contain their own means of HTML generation, built into the library itself. 

If you put it like that, it seems a little heavy-handed for a form library to also include an HTML or templating library. On the other hand, a form library has as one of its goals the correct generation of a very specifically configured set of HTML tags and attributes.

—*Wednesday 2nd June 2021.*
