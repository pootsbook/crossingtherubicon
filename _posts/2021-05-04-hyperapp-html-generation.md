---
layout: post
title: "Hyperapp: HTML Generation"
---

While we are on the subject of simple JavaScript frameworks, or anti-frameworks, let me introduce you to [Hyperapp][ha]. Hyperapp is a micro-framework for building client-side JavaScript applications. It has 375 SLOC and is around 1 kilobyte of JavaScript. I believe it takes inspiration from the Elm Architecture.

What’s most interesting at this stage is the way in which you write HTML. It looks very similar to the API we started with in [A Small HTML Helper Library][ash] which is no surprise. This is an example template, written in JavaScript, not a templating language:

```
h("body", {}, [
  h("h1", {}, text("Hello, World!")),
  h("p", { class: "hero" }, text("HTML with Hyperapp"))
])
```

Which is equivalent to:

```
<body>
  <h1>
    Hello, World!
  </h1>
  <p class="hero">
    HTML with Hyperapp
  </p>
</body>
```

It’s an identical API but this time in JavaScript and not Ruby or Elm.

The implementation is slightly different though, thanks to the fact that it is being rendered on the client-side, and also taking into account the need for performant DOM mutation. It has a viritual DOM implementation. So Hyperapp is first creating nodes on a virtual DOM before rendering the virtual DOM to HTML.

It ultimately uses browser DOM APIs `createTextNode` and `createElement` but it starts with `createVNode` which builds an interim data structure representing the node, a JavaScript object which plays the role of a branch on an abstract syntax tree (AST).

It’s interesting to see tools and libraries in different languages using this approach to constructing HTML.

—*Tuesday 4th May 2021.*

[ha]: https://github.com/JorgeBucaran/hyperapp
[ash]: https://www.crossingtheruby.com/2021/03/06/a-small-html-helper-library.html
