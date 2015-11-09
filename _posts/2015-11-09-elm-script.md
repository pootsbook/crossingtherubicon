---
layout: post
title: Elm Script?
subtitle: Poots Ponders Parent and Progeny
---

A friend asked me to elaborate on why I hadn’t included ClojureScript alongside CoffeeScript and TypeScript in a [previous post][enthralled], and why I alluded to the significance of the fact that Elm was Elm and not ElmScript. The omission was deliberate and my thoughts on ‘Script’ are speculative but my answers to both questions are inter-related and hopefully by going into detail you’ll be able to see what I was getting at.

First of all, I see a clear division in transpiled languages between those that are superscripts and superstructures<sup>1</sup>. Both sit above (_‘super’_) JavaScript but there are key differences in their goals, characteristics, and implementations. On the one hand there are those that seek to make working with JavaScript more pallatable by adding advantageous affordances and on the other those that seek to replace JavaScript with browser implementations of server-side languages.

CoffeeScript, the trailblazer, was conceived as a thin (and beautiful) veneer making JavaScript more accessible and approachable by giving it a syntax more like Ruby or Python. In addition it smoothed the jagged edges and codified best practices enhancing the experience and helping avoid the more egregious pitfalls. TypeScript is similar in that regard but with a different focus. It allows you to put JavaScript in a straight-jacket via type safety thus appealing to the familiarity of Java or C#. There is an added bonus of deep integration with existing toolchains, extending said tools’ productivity gains to JavaScript. Both maintain by and large the same semantics while introducing certain benefits.

The other side, that of compiling existing server-side languages to JavaScript are best exemplified by ClojureScript (Clojure) and PureScript (Haskell), or, to a lesser extent, Opal (Ruby). That is, a valiant attempt to preserve the existing semantics of a known language in a different environment, using JavaScript as a means to that end, and rendering it an incidental assembly language.

You could say superscripts (transpilation) are putting lipstick on a pig and superstructures (trans-compilation) are trying to squeeze a stallion into a pig skin—which is effectively to say, on both counts, close but no cigar. In other words one camp goes too far, and the other not far enough. One doesn’t fully redeem JavaScript by dressing it up, and one doesn’t respect the browser environment enough by leaving JavaScript in the closet.

Wouldn’t it be great if we could take the best aspects of both approaches, fusing them into something that was better suited than JavaScript but also considerate of the peculiarities of programming in the browser environment?

I believe that Elm is that third and middle way, qualified to supersede the ’Scripts by providing a language and an approach for our present predicament. It transcends the others precisely because it is attendant to the particulars. It does justice to history, not by repeating it, but by reimagining it; and to the present not by swimming with the tide, but by channeling the flow. Elm is the future.

So it deserves a name that stands alone.

—*Monday 9th November 2015.*

<div class="footnotes">
  <ol>
    <li>
This is a gross simplification of the rich and varied ecosystem that has blossomed in the furrow that Jeremy Ashkenas ploughed with CoffeeScript.
    </li>
  </ol>
</div>

[enthralled]: http://crossingtheruby.com/2015/11/06/enthralled-by-elm.html
