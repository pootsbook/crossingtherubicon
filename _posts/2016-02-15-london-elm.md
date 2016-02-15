---
layout: post
title: 'Elm London’s First Meetup'
subtitle: Poots Passage to the Pommy’s Principal Place
tags:
 - Elm
---

On Wednesday last week I flew over to London from Amsterdam to attend the newly formed Elm London’s inaugural meetup. I was glad to have RSVPed early as the event quickly became oversubscribed with the waiting list outnumbering the attendee list three to one. That in itself is a clear indication of the kind of interest in Elm from all quarters.

The draw was well articulated in the event description with two talks—from Claudia Doppioslash ([@doppioslash](https://twitter.com/doppioslash)) and Noah Hall ([@eeue56](https://twitter.com/eeue56)—and an explicit part of the program for talking about how great Elm is! A nice touch, and it certainly didn’t leave any of us lost for words (although some of Noah’s presentation did).

### What’s Elm? Why should I be interested?

Given this was the first meetup in London, Claudia was to give an introductory presentation, entitled, ‘What’s Elm? Why should I be interested?’ I arrived a couple of minutes late so I missed the start of the talk but I was there for the lion’s share. Claudia chose to focus on what makes Elm unique, highlighting the time-travelling debugger and going into a bit of detail about how and why it was possible, which brought us into the details of Functional Reactive Programming with signals. I think if I had been brand new to Elm I might have struggled to see the forest for the trees but given my interest and (basic) experience I found some of the detail very timely. I believe many in the audience were in the same boat, having played with Elm already if not plotting how to get a foothold for Elm in their production stack.

What I found useful was that Claudia didn’t limit herself to `StartApp`, explaining how signals were folded from the past with `foldp` to arrive at the current state of the application. The fact that all of a signal’s values exist is the basis for the capability to step back in time. She also showed off some more advanced signal mapping with `map2`, applying a function to two distinct signals. Also covered was the functional foundation for many of the features in Elm and the Elm Architecture, calling the combination of the Union Type, Pattern Matching and Maybe the functional triad. The most obvious use-case is in the `update` function, which pattern matches on the Action type (a Union type), and we saw a few examples.

All-in-all a very enjoyable peak behind the curtain, although more of a what and how than why; to those of us who have used Elm the benefits are obvious. Claudia also has a great blog, *LambdaCat*, which I have found very helpful, especially the “[Road to Elm](http://www.lambdacat.com/road-to-elm-index/)” series, and recently made a podcast appearance on [Functional Geekery](https://www.functionalgeekery.com/episode-42-claudia-doppioslash/).

### Experiments in Elm

Next up was Noah Hall from NoRedInk, who had just come back from visiting their offices in San Francisco. Noah is a modern day ‘digital nomad’ profiting from a remote working policy that allows him to travel. I believe the fact he was visiting the UK was the catalyst for getting a meetup together and it looks like he’ll be delivering a similar presentation to the first [Stockholm Elm meetup](http://www.meetup.com/Stockholm-Elm/events/228463568/) this Wednesday. As the flagship Elm-in-production company, it is always sure to draw a crowd of people who are interested and sometimes even invested in hearing how Elm stands up to production usage *(spoiler: it does pretty well)*.

The title of his talk was ‘Experiments in Elm’, and reflected his role of Elm-Ops at NoRedInk, which appears to involve pushing Elm to the limit. He showed off three bits of his work/research with the caveat that these were just three of ten, the other seven at such a stage that NoRedInk were not yet at liberty to show them off. Talk about keeping us in suspense!

#### Elm Package Performance

The first of the three was a very practical concern about improved performance for package downloading from `package.elm-lang.org`. NoRedInk run hundreds of CI builds a day and each comes with a clean environment onto which packages have to be downloaded. From what I gathered the infrastructure around the package system is essentially just GitHub and downloading repository tarballs is a particularly laboured process which slows up builds and very often leads to timeouts and failures. A longer term solution is in the works, but for now, Noah has put together a Docker image that will allow you to put a cache in front of GitHub. He did a demonstration of the difference in speed between a cached and a non-cached fetch and there was absolutely no comparison.

#### Full Stack Elm

The second bit was a summary of the takeaways that Noah has uncovered from his experiments running Elm on the server via the [take-home project](https://github.com/NoRedInk/take-home). This is a proof-of-concept full-stack stack Elm project, meaning everything from build tools and server-side code to client-side code and stylesheets are all written in Elm. I’d heard of server-side Elm experiments on Node.js but I hadn’t quite appreciated the scope nor the ramifications of such a move. Noah made it clear that an actual, robust implementation was at least a couple of years away and then probably not on top of Node.js but even so, the potential benefits he provoked are enormous. Aside from the obvious attraction of writing everything in the same language (that’s not JavaScript) what I found compelling was:

- the opportunity to use the same, elegant, constraining architecture on both server and client. That’s right, FRP and the Elm Architecture fits the server too.
- sharing types across client and server, with type-safety across the boundary (no need for JSON decoders etc.)
- database support
- a web server in Elm
- pre-rendering on the server


#### Action Logging, Mirroring, Replay

The third part of the talk was the one which left us all in the audience a little shell-shocked (and apparently this wasn’t even the most exciting of the ten, so what’s coming upstream in the Elm pipeline must be pretty special). Noah opened two browsers side-by-side on a local instance of the NoRedInk application, started a server process and proceeded to take a few actions in one browser *that were perfectly mirrored in the other*. He moved to that second window and continued with activity being mirrored back to the first. If that wasn’t enough he pulled out another window from the background to show off *a comprehensive log of the actions being taken*. I can’t remember clearly if there was more to it in the demo, partly because my mind had jumped off to imagine the latent potential that flows out of that kind of activity logging:

- **serialization of historical activity and state for reproduction**: think of how long it takes to recreate errors and locate bugs, by trying to mind-numbingly step through the application and capture the precise state that triggered the error. In principle you could send this off to the developers / QA team when the error is triggered and have an instant environment *that you are able to step backward and forward in time* to precisely locate the bug. 
- **automated manipulation of the action log**: if you can generate the action log from the app, then in principle, you should be able to go the other way, i.e., script the app by manipulating the action log, without the tedium of a selector based approach freeing you to reason and interact at a higher level. I’m thinking more elegant implementations of the Gherkin–Code bridge in a typical Cucumber setup among other things.

The use-case for activity mirroring was focused on QA, no doubt a useful factor for NoRedInk, but take a step back and you can see how this kind of technology could be used in low-bandwidth connections to adopt ‘screen-sharing’ without the massive overhead of video streaming.

I managed to catch a glance of action log manipulation in the pub afterwards and the craziest part of the whole affair is that this capability was introduced to a typical Elm application *in under twelve lines of code*. It is yet another indication of how the characteristics that make Elm, Elm, **remove whole classes of problems** on the one hand and on the other, **act as a powerful enabler for interactive programming**. 

### Meetup Host

I’ll have to finish here and maybe write up some of the content of our post-meetup conversations at a later date. Before I close though, a big thank you to Claudia and Noah, both for their presentations and taking the time to enthuse us all about Elm afterwards. A big thank you has to go to Sébastien Varlet ([@svarlet](https://twitter.com/svarlet)) and his company [Plumbee](https://www.plumbee.com/) for being excellent hosts. The space was fantastic (fuusball, pool) and we were brilliantly catered for (fresh fruit, hors d’oevres, pizzas with all the trimmings—the French know how to serve food (although to be fair to Seb and the French, Papa John’s pizza might not be top of the list ;) )). With a spread like that I might have to make London a regular stop.

—*Monday 15th February 2016*

*If you’re interested in more Elm, I volunteered myself to give a Webinar to introduce Elm to the LondonJS community on April 14th. [Sign up here](http://www.meetup.com/London-JavaScript-Community/events/228773798/).*

