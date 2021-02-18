---
layout: post
title: The Limits of Mechanical Sympathy
---

In a previous post I offered a short definition of [mechanical sympathy][ms] as applied to programming. Although I argued that abstractions permitting the exercising of mechanical sympathy are beneficial, there are of course limits to how far you can draw this conclusion.

I used the analogy of automatic and manual transmission. A friend pointed out the following:

> WRT [the] car analogy, just recently I saw a strange phenomenon. To enter our parking you need to drive up a really small incline but it got really icy. The road is narrow and there are a lot of people walking there too. I saw three or four drivers that needed to stop and then couldn't move. They had to drive back to where it was flat and then create some momentum. I called my wife that if she can, she should avoid stopping there. Of course, when she was near, she had to stop because someone walked their dog without a leash. The dog was walking in the middle of the road before the owner called him. And then, miraculously, my wife continued without a slip.

> We have an automatic transmission

> Probably most people who have a manual one didn't know how to take advantage.

> For mere mortals, the abstractions might be very helpful :D

Now there is no doubt that the quality and effectiveness of automatic transmission has risen with the technological tide since I last drove an automatic around seventeen years ago, so perhaps my analogy wasn’t the best suited. Nevertheless it is a good demonstration that there are abstractions that improve the status quo in a manner better than we could hope to ourselves.

An example that comes to mind is compiler optimization. This is something that the computer is incredibly good at. And no amount of our jigging and poking could produce a result better than the computer. Now someone has to write the compiler, but we do not need to have any low-level knowledge of it to reap the benefits.

Indeed, taken to an extreme, the ultimate mechanical sympathy would be to write our code in ones and zeros. So there is a range of factors to be taken into consideration here before we _de facto_ rule out abstractions that conceal inner workings in a black box.

One of the reasons I enjoy writing Ruby and Elm for example is precisely because they allow me to think at a higher level. Believe it or not, I have got through my career without the foggiest idea how to manage memory or write a line of C. And I likely never will, despite my intellectual curiosity.

Elm saves me from JavaScript, Ruby saves me from C and manual memory management, garbage collection and the like. C saves you from assembly. Elixir saves me from Erlang. And so it goes on. 

But Elm allows you to use ports to interact with JavaScript; Ruby allows you to write C extensions; Elixir allows you to write Erlang. I think in these cases it is precisely because they offer you a level of control, being able to step ‘inside’ that appeals to me.

Syro allows you to easily build on Rack and create URLs straightforwardly. Rails offers similar, but good luck pulling apart the engine.

–*Thursday 18th February 2021.*

[ms]: https://www.crossingtheruby.com/2021/02/17/mechanical-sympathy.html
