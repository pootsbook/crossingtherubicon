---
layout: post
title: "Forward Planning"
---

I was driving past some roadworks this morning and it dawned on me that what they were working on was simply the next step in a plan that had been thought out many years ago.

About twelve years ago in fact. I had been driving down the same road for the very first time. It was my first trip to my (now) wife’s home town in the Netherlands. The whole experience of the incredibly polished Dutch infrastructure during my first forty-five minutes in the country had already been truly eye-opening but what appeared next stuck out like a sore thumb.

There was a bridge over the road that was double the width of the road! And I wasn’t about to drive under the middle of the bridge, but only under the left-hand side. The other side stuck out into a field as if they’d let a spatially challenged trainee engineer loose on the project. It was a sight to behold.

I turned incredulously to my wife, half laughing:

Poots: “well, they got that one horribly wrong.”

Wife: “what do you mean?”

Poots: “the bridge, look at the bridge, it’s too big!”

Wife: “what are you talking about?”

Poots: “They’ve built a bridge double the width of the road, look”

Wife: “Ehh, what’s so strange about that?”

Poots: “…”

I was a little non-plussed that she (a) didn’t see the funny side, and (b) didn’t in the least bit think that there was anything strange about this monstrous appendage polluting the otherwise pristine landscape.

After we’d established that I did not think that this was normal, my wife said, very matter of fact, “well, it is normal—they’re planning to enlarge the road here in ten years time so they made space for it.”

---

This is called foresight. Thinking ahead. Not doing things by the seat of your pants. Being proactive, not reactive. Having a plan. In the UK they’ll lay a new road and then start drilling it up again the following week to lay a pipe. 

For example, in London they learned of the Dutch road-calming measures and dropped speed bumps on top of the roads [all over London][sb]. It later transpired that over one in five cars in the UK [reported damage][rd] to their cars as a result of speed bumps, with the majority of the speed bumps and the reported damage being in London.

A speed bump in the Netherlands is built into the road. It is part of the road. There is no distinction between the road and the bump, apart from the elevation. It is the same asphalt, laid at the same time, with no change of surface. The angle is enough to force a reduction in speed, but not so much that it will cause damage.

A speed bump in London is asphalt or concrete dumped in a pile on top of an existing road. Little thought was given to what impact it might have on a car. “They do it in Holland,” they thought, “and it seems to get the job done.”

In the Netherlands these things are forethought; in the UK they’re an afterthought.

---

So today, there I was, driving along that very same road, marvelling at the fruition of this particular Dutch plan. They were laying the “other half” of the road to go under that bridge—twelve years later. And I thought to myself, in the UK they’d probably have built a narrow bridge first, later knocking it down only to build it again to fit the updated road layout.

Now this post isn’t about bridges, or speed bumps, or the Dutch, or the British—it won’t even do damage to your car—but it is about forward planning. About being deliberate. About doing things now that cost more but will pay off in the long term.

I thought about language design, about Elm, about functional programming, about `Maybe` and about `Result`.

These things ‘cost more’. You can’t ignore them. You have to deal with them. But if you do all of that up front, then you have less to worry about further down the line.

Elm certainly does this, and I feel that functional programming (irrespective of language) also does this.

In Elm you have to think explicitly about failure, not just about the happy path. You have to model your application with `Maybe` and `Nothing` and `Ok` results and `Err` results. It’s a little tedious at first, but once its in place you won’t have to revisit it.

Of course, I’m not saying that with Ruby I can’t do the same thing. But I’m not encouraged to do it, and I’m not forced to do it. I’m left up to my own devices: my own error handling mechanisms; my own understanding of what potential errors might be raised. It’s not encoded. And more often than not, it is left as an afterthought.

And, later, I’ll have to dig up the road as it were; follow a code path while tearing it open to lay an error handling mechanism for something I didn’t think of. That’s messy. It costs a lot more time than if I’d put it in at the start.

And generally, functional programming forces you to do more up front thinking. The focus on data structures means that you think carefully about what you are modelling to get it right first time rather than leaning on the malleability of objects.

So code like the Dutch do roadworks, and not like the British.

—*Friday 5th February 2021.*


[sb]: http://www.bbc.co.uk/insideout/content/articles/2008/10/29/london_speed_bumps_s14_w7_feature.shtml
[rd]: https://www.rac.co.uk/drive/news/motoring-news/drivers-report-speed-bump-damage/
