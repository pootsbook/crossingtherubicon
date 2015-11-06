---
layout: post
title: Enthralled by Elm
subtitle: Poots’ Programming Preference
---

I have never been quite as excited about a programming language as I am about Elm. It’s been only two weeks since I first set eyes on it, yet I am completely and utterly sold. The last time I had a comparable experience was when Ruby with Rails convinced me by their elegance and fitness for purpose that I should in fact learn to program and make a career out of it.

Of course then the landscape was PHP and Java, so Rails didn’t have to do much to offer a better alternative, but it did that and more. The world of web development today is more akin to a technological Cambrian explosion, with the paradox of choice precipitating a paralysis in the programmers’ psyche.

Elm has come to rescue you from that existential angst and the whimsical vagaries of JavaScript. It provides you with something so compelling that the alternatives vanish from sight. At least, that’s what it feels like to me. Granted, I’ve never really taken to JavaScript as a language, and although I’ve resolved to ‘learn it properly’ on a several different occasions it never really clicked in the same way Ruby did: there were always a host of ways to do the same thing; you had to force yourself to use one paradigm or the other; and people still debate whether or not we should be doing things in an object-oriented, functional or imperative style. Or why not a smorgasbord of all three?

JavaScript’s malleability becomes a quagmire for the uneducated and the ill-disciplined. Yet it is precisely this quality that allows it to become such a good target for transpilation. Elm sits in a tree with the likes of CoffeeScript or TypeScript in that regard. The key distinction is not that it omits _Script_ from the name (the significance of which I will discuss another time) but that it draws on a rich tradition of functional programming with a focus on concurrent Functional Reactive Programming<sup>1</sup>. I know little of either paradigms but I am guessing this is why Elm feels particularly tight (constrained, compact) and discloses scrupulous deliberation in its design. Elm removes ambiguity and reduces cognitive load, but in a way that is simple rather than simplistic.

That’s not to say it will necessarily be easy to master. I’m going to have to recalibrate my thinking to really become effective. But at no point have I had the feeling that it won’t be worth it—quite the opposite—Elm promises to educate me, and to leave me better off. 

It is, however, easy to start. [Evan Czaplicki](http://evan.czaplicki.us/), the author of Elm, has done a fantastic job in lowering the barrier for entry, from running your first program in the [online editor/compiler](http://elm-lang.org/try) to hiding some of the more advanced concepts behind [`StartApp`](https://github.com/evancz/start-app), a module made specifically for beginners.

It’s impossible to do Elm justice in a few paragraphs. I hope to make up for the sparse technical detail in later posts, but I recommend in the first instance that you try it and experience it for yourself—but be careful, you might just end up in Elm’s thrall.

—*Friday 6th November 2015.*

<div class="footnotes">
  <ol>
    <li>
The definition of FRP is narrow and the term has suffered some semantic diffusion (how about <a href="http://stackoverflow.com/questions/5875929/specification-for-a-functional-reactive-programming-language/5878525#5878525">Denotational Continuous-Time Programming</a> instead?). See a talk by Evan Czaplicki for an overview, ‘<a href="https://www.youtube.com/watch?v=Agu6jipKfYw">Controlling Time and Space</a>’ or §2.1 of his senior thesis, ‘<a href="http://elm-lang.org/papers/concurrent-frp.pdf">Elm: Concurrent FRP for Functional GUIs</a>’. The technical terminology can be more of a hindrance than a help so don’t get hung up on it.
    </li>
  </ol>
</div>
