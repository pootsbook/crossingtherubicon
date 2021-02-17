---
layout: post
title: Mechanical Sympathy
---

I mentioned previously in [Rails Routing Code Complexity][rrcc] that I now fall firmly into the camp of people that “like to know what is going on under the bonnet, how the engine works.” This is based on the conviction that knowledge of the lower level yields benefits at the higher level. 

It’s a bit like working with, rather than against, the grain. You are using the material in a way that suits the material. As a result the process is more straightforward and you often arrive at a better end product.

You can swim with the tide or you can swim against the tide. The former takes advantage of the natural flow, propelling you along and saving you energy. The latter costs you extra energy and will take far longer.

I first heard the term ‘mechanical sympathy’ being applied to this principle in software development at EmberConf 2015 in a talk by Stefan Penner on Ember.js Performance ([abstract][epa], [video][epv]). I seem to recall that he talked about the performance benefits Ember’s runloop could extract when rendering by being sympathetic to the browser’s internal operation with `requestAnimationFrame`. That is, rather than signalling a render on each change, changes could be batched and run just before the browser would redraw. He actually talked about how the JavaScript VM dealt with object shapes and how Ember could be sympathetic to these inner workings to extract more performance.

Mechanical sympathy is, as it says, being sympathetic to the mechanics of the machine you are operating. It is often applied to the world of motorsport and geared towards extracting the maximum amount of performance out of a car. Sir Jackie Stewart, the British Formula One racing driver and three-time world champion (1969, 1971, 1973) phrased it as follows:

> the best drivers had enough understanding of how a machine worked so they could work in harmony with it

And this continues to the present day. Toto Wolff, Team Principle of the Mercedes Formula One team says often of seven-time world champion Sir Lewis Hamilton that, as he matured, Sir Hamilton added an in-depth knowledge of the car to his raw racing talent in order to extract ever greater levels of performance. 

An example of how the details matter is apparent in a recent controversy (in Formula One) with Pirelli tyre design. A particular design had a tendency to overheat which reduced the ability to race flat out. The only option was to ensure the tyres operated within a given temperature window to avoid tyre wear. Think also of understanding how a racing car handles corners so that you can adjust breaking distance, entry speed and exit acceleration to ensure optimal performance.

Closer to home, a more recognizable example might be the difference between a traditional car with automatic transmission and one with manual transmission (a hand operated gear lever with clutch, known colloquially in the US as “stick shift”). With an automatic you have no control over when a gear is shifted. Manual transmission allows you to choose when to shift. Manual transmission makes the inner workings more accessible to you, and a knowledge of this inner working allows you to take advantage of it in a way that is impossible with automatic transmission. You are able then to use this mechanical sympathy when judiciously braking into corners with aid of the gear box, choosing the appropriate gear on a downward start, having more control taking off in icy conditions and the like.

The application then to programming and abstraction becomes clear. You can develop a mechanical sympathy for your tools or for the underlying platform by understanding how they work. This understanding can give you certain advantages over an ignorance of what is happening at a deeper level. We have seen the benefits of understanding the [basics of HTTP][http] and how [Rails obscures][ro] the process of creating a URL path while [Syro elevates][se] the tree-oriented structure of URLs to be the core abstraction.

In some ways this resembles the automatic and manual transmissions. One is more convenient and invisible (Rails) while the other lends more control and flexibility (Syro). My feeling is that the more your tools are faithful to the underlying concepts they abstract then the better this is in the long term. It gives you at the very least the opportunity to exercise some degree of mechanical sympathy.

—*Wednesday 17th February 2021.*

[rrcc]: https://www.crossingtheruby.com/2021/02/16/rails-routing-code-complexity.html
[epa]: https://2015.emberconf.com/speakers.html#spenner
[epv]: https://www.youtube.com/watch?v=iTZxo6bSx44
[http]: https://www.crossingtheruby.com/2021/02/09/framework-abstractions-http.html
[ro]: https://www.crossingtheruby.com/2021/02/10/framework-abstractions-http-rails-routing.html
[se]: https://www.crossingtheruby.com/2021/02/11/framework-abstractions-http-syro-routing.html
