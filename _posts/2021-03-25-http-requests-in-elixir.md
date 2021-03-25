---
layout: post
title: HTTP Requests in Elixir
---

Back in January, I was exploring the design of HTTP libraries in Elixir with [HTTPoison][htp] and [Finch][fin] and using them as examples of [_operations as data_][oad]. At that time I turned to friend and former colleague [Wojtek Mach][wm] for insight since he knows a thing or two about Elixir.

We talked about the design of HTTP libraries in Elixir and discussed the trade-offs of different approaches. He made me aware of Finch and helped me to understand a few of its finer points.

Well, my posts and our conversations seemed to have created an itch that he just had to scratch. Despite admitting that writing and maintaining an HTTP library is a fool’s errand, he has taken the bait and is building a library on top of Finch.

I’ll wait until the library sees the light of day before taking it for a test-drive and examining what’s going on under the hood. (Maybe that day will never come as he sees sense and pulls out while he can, but that would be a shame.)

Despite that, I wanted to share it as an encouragement to anyone who is on the fence about blogging. I have been writing daily for almost three months now, and although I didn’t write them, I can now count two libraries, one in Elixir (the subject of this blog post), and one in Ruby ([Hypertext][ht]) which came into being, at least in part, as a result of this writing or conversations about the themes contained within it.

It has surprised me. Pleasantly so. I may not be the one crafting the code, but writing and conversing have provided the earth and the seed out of which others have grown plants. Long may it continue.

—*Thursday 25th March 2021.*

[htp]: https://www.crossingtheruby.com/2021/01/18/operations-as-data-httpoison.html
[fin]: https://www.crossingtheruby.com/2021/01/21/operations-as-data-finch.html
[oad]: https://www.crossingtheruby.com/2021/01/17/operations-as-data.html
[wm]: https://twitter.com/wojtekmach
[ht]: https://www.crossingtheruby.com/2021/03/14/hypertext-write-html-in-ruby.html
