---
layout: post
title: Why Do We Need Elm?
---

<small>
  <em>
    Penned on 18th January 2016
  </em>
</small>

The title could just as well refer to any good solution to the problem I shall unwrap in this post, but I believe Elm does a pretty good job of it, if not the best. The reasons for that I have explored elsewhere, so it need not detain us here, but suffice to say it contains the right ingredients in the right combination at the right time.

The last of these is perhaps most relevant because the question in the title could be suffixed with ‘now’, why do we need Elm _now_, and not, say, at some point in the distant past or future? As always, by looking at the past we will understand the present, and by looking in the past with intelligence we will discern the future through which we can inform our present.

Now we can look at the past with different lenses, focusing on the broad arc of the history of computer science and software engineering, but I would like instead to consider the recent move from largely server-generated pages to full blown application-esque interfaces and programs running largely on the client. No discussion could be held without the large three hundred tonne elephant in the room (that’s a metric ton), JavaScript. Much maligned on account of its imposition, much dissected by means of inquisition, the scripting language of the web is impossible to ignore. Ironic then that much of what will here transpire will encourage us to do exactly that, for the reasons I shall now set forth.

Let’s wind the clock back a few years to the advent of the back-end MVC framework. Rails came with many bells and whistles and, what is more remarkable, managed to get them all to play in tune. The methods that were first employed were necessarily frowned upon (RJS anyone?) and with subsequent releases of the framework, unobtrusive JavaScript had firmly planted its feet with a sensible jQuery and JSON decoupling from RJS and server-rendered partials.

Now in the beginning, the grounds for including most JavaScript were quite superficial. Prototype and Scriptaculous specialised in adornment, with effects and animation the mode du jour. Later with jQuery and JSON the objectectives became more substantive, avoiding roundtrips to the server, or avoiding a large rendering time with all-in-one database queries, by spreading things out asynchronously with AJAX (or AJAJ, but that never quite had the same ring). This was the time of the great sprinkling, after a renowned sage coined the approach with the term ‘sprinkles of JavaScript’. Ice cream was your bread and butter and the sprinkles were but a grudging acknowledgement to the excesses of youthful immaturity. But people liked the sprinkles and continuted to drop more and more on, and we all know what happens next. 

We have all, I am sure, had the harrowing experience when the man in the ice-cream van hands us a delicious soft ice with so many sprinkles that some of them start falling off. The horror! You desperately start attacking the scoop to avert the avalanche, but to no avail, there will always be those that escape your grasp. And true to our nature, our parents looked on despairingly as our initial delight turned into the deep despondency of desiring the small bits that had escaped us instead of being more than content in the large delicacy that we had in our hand—now slowly melting onto our hands as the tears welled up.

Okay, okay, so I don’t know if JavaScript has made anyone cry but the desire for more sprinkles is real. The Dutch have gone so far as to turn chocolate sprinkles into a household product for pouring all over your bread. Now the problem with sprinkles or JavaScript used in this way, is that the pieces are all small and disconnected. If you want to access the same bit of ice cream then the only solution is to push one out of the way for the other. Some drop off, other bits get broken and so your disparate collection of program parts really needs a Spine or to grow a Backbone. (Maybe that’s why the Dutch switched ice cream for bread, its a bit more manageable.)

And so the story went on, we solved the state problem with models and AJAX but the view became problematic. So we went to two-way bindings (Knockout, Rivet) to better manage handling view updates. This didn’t perform very well so we tried dirty-tracking (Angular), using observers (Ember) and pushed for `Object.observe` to be built into JavaScript natively. And then we discovered that to get around the bottleneck of the DOM for updating the view, we need to abstract it with a virtual DOM (React). And then last, but not least, we opted for one-way data flow because it simplified the whole model, made our programs simpler to understand and to manage.

The orthogonal, but no less instrumental, enterprise of stitching all these pieces together effectively gave birth to the framework ecosystem itself; from the confusion about how best React should fit application parts together to the more all-in-one approaches with first-class support for build and CLI tools in Ember and Angular. These last also defined plugin semantics for deep framework integration and easy code re-use.

Now we have come so far it begs the question of the Emperor’s Old Clothes—he’s wandering around with so many clothes and associated paraphernalia that the crowd are left wondering why on earth it is all necessary. Our approach to maintainability is founded on our predilection to add more things to aid with maintainability when adding the things themselves actually increase the burden.

And this is where Elm drives the stake into the heart of the enemy, entropy, and affords us with a fitted battlesuit designed to conquer the complexity, not only in our applications, but the frameworks and tools we have built to serve those applications. For Elm, with foresight, has solved many of these problems in a cohesive whole in a way that we as a JavaScript community have, until now, not been able to. For the maintenance burden of changing tools is just as great as the maintenance burden of no tools at all.

Elm seems to have hit the nail on the head with respect to the application of known solutions to old problems onto new problems, or, at least, onto old problems in a new environment.

—*Thursday 14th January 2021.*
