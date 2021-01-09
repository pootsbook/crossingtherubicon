---
layout: post
title: "Badass Developers: Elm"
---

<small>
<em>Penned on 15th January 2016.</em>
</small>

And design a new langugage is what Evan Czaplicki did, and boy does it solve a lot of those problems. It certainly puts paid to the idea that progress comes at the cost of the layman’s experience. Granted, Elm is perhaps not as fully featured by for what it does, it does it very well. 

Mr. Czaplicki has said that if you know ML then learning Elm will take you a day. You probably don’t know ML, but learning Elm won’t take you much longer as a result. Elm is almost designed for paedagogy and the learning curve is punctuated by only two concepts that would challenge a working developer. Signals, and Maybe (the Option type). But this doesn’t create a barrier because the emphasis is constantly being placed on getting your hands dirty and jumping in to build something. There is a shift to a more functional style which can normally send most running for the hills but the particular focus on practice means you don’t have time to think about what you are doing.

The gap of suck is therefore absolutely tiny, and when you have seen the Elm Architecture in the small, with trivial examples, due to its fractal nature, you know it in the large. That the language forces you towards that architecture means there is little chance of going wrong or losing your way. The functional reactive model is a small leap but once you’ve made it it gives you a simplicity on the other side of complexity. Your application is just a series of steps through time and your user interface is a stateless function of application state. All you are really doing is writing how the state should update as it reacts to a bunch of different inputs, then, as a completely independent step, declare how your user interface should look. The range of inputs can vary in size but the model or flow remains exactly the same. You can have an island on a server side rendered page or a single page application, the architecture is the same.

The genius from a learning perspective is that there is a clear path to the top, but at each step of your journey you have something to show for your work, and in many ways the complexity stays constant. Now Elm’s compelling context is building rich, interactive graphical user interfaces, you can tell the fun that people are having when they spend their time building games. Perhaps the greatest compliment that you can give Elm is that the biggest barrier to learning it is getting derailed, not by frustration, but by stopping to play all of the games that people have built with it.

You are also never dropped back into the gap of suck by upgrades or unexpected version incompatibilities. Due to the type system and the package manager it is impossible to upgrade to a version that is incompatible, and if you do try, it will tell you exactly what has changed and therefore what you need to fix. There is no fear and uncertainty down to churn, and even if there were breaking changes the compiler would assist you in fixing them.

Elm has a (small) user base that is fanatical about it because it makes them feel like superheroes. No runtime exceptions; if it builds it will probably work; no time-sink debugging; less code to maintain; no state; managed side-effects. This is a benefits list and it may be surprising that the corresponding feature list reads: type system; immutability; Signals (Behaviour/Events/time). 

The meat and potatoes of functional programming cooked in a way that everyone can eat.


—*Saturday 9th January 2021.*
