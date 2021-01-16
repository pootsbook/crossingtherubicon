---
layout: post
title: Elm and the Ill-Fated Object.observe
---

<small>
  <em>
    Penned on 19th January 2016
  </em>
</small>

I believe that the JavaScript ecosystem and Elm have significant differences in the manner of their design, and none can be more prominent than the story of the ill-fated `Object.observe`. For those that are not aware, `Object.observe` was a feature suggested by members of the committee that steer the development and therefore the future of the JavaScript language. The primary aim of the feature was to enable in-built observation of property changes in JavaScript objects, with automatic change notification that would act as a replacement for techniques like dirty-checking or a custom observer system running on top of plain JavaScript objects using something like a meta-object. The case being that both of these approaches were inefficient and cumbersome and the use-case was common enough that it was worth modifying the language itself to incorporate this feature.

So if it was so worthwhile, why, after initial acceptance and a period of trial, did `Object.observe` languish in the graveyard of bad ideas? Was it not such a good fit after all? Let’s give some smart people the benefit of the doubt. The reality is that it may have been good, but there was something superior that invalidated the need for such a feature the first place.

It is a good example of complexity being replaced with simplicity. To understand we have to look at the context of the feature. The domain was the user interface, and the problem was re-rendering the UI whenever the data changed. How was the UI to know when and with what to re-render? There needed to be some way of notifying the UI code to update whenever the data changed.

The solution in effect was to give the data tentacles (observers) that could reach down in to the UI to connect each place where a piece of mutable data was being used and to notify the UI of mutation so that it could change what was being displayed. The data bindings could be said to be two way so that if a data change came from the server the change would flow down one-way, but if the change would occur in the UI component itself then the change would flow up the other way.

Now, with the benefit of hindsight we can see that this approach is the height of tight coupling and there is a distinct lack of encapsulation. The data can be modified anywhere by anything in the system and then you trust a bunch of hamsters to run around your program and update everything appropriately. React came along and blew the assumption that things had to be done this way out of the water (getting rid of the octopus, and the submerged hamsters). The key insight was to clearly separate concerns and make the UI a stateless function of application state. That is, the UI had no responsibility for keeping itself updated, but instead the application would just hand the UI a new state and it would render itself with the new data. By moving to a more functional (immutable) style it invalidated completely the need for any kind of observer system baked into the objects themselves. Both Angular (2) and Ember (DDAU) embraced this simpler and superior pattern to great success and the need for `Object.observe` was obviated.

What can we learn from this lesson?  I think there are a few things. Complexity can often be solved with simplicity—the simplicity on the other side of complexity, also known as profound simplicity. This does not often arise from groupthink (a committee or a core team) or from a local maxima. Intelligent design is better than evolution; that is, a considered approach by a sharp mind is better than many flailing in the darkness, and that often the aptness of a particular language or framework is the cogent application of a single mind, the benevolent dictator, where contributions are brought into the sphere of reference and not hodge-podged willy-nilly.

The caveat is that we need to flail around in the darkness to gain an understanding of the problem, and without this we cannot necessarily come up with good solutions, but having done so the improvements are often not incremental, but jumps, based on the application of a different pattern or paradigm.

The examples are too many to mention, but here are a few: Matz with Ruby; DHH with Rails, Jeremy Ashkenas with Underscore, Backbone, CoffeeScript; José Valim with Elixir; Linus Torvalds with Linux, Git; Steve Jobs, Jonny Ive with Apple; and pertinent for our purposes, Evan Czaplicki with Elm. All of the above are intelligent designers. All of the above had a philosophy which drove them to the character and particularity of their solutions.

In most of the above cases they are working on a web or bed of complexity and they have taken the time and delivered the insight to master that complexity, to come out the other side of that complexity and present to us simplicity, profound simplicity. A simplicity that delights because the solution fits the problem like a well worn glove or shoe, all the parts match up comfortably, there is nothing more to do. We hide ourselves in shame because the answer was obvious, yet we never saw it, or because it surpasses what we thought was even possible, causing a deep appreciation.

The people above have often escaped groupthink to get to where they are. The level of thought that degenerated to the lowest common denominator or appears as the result of group dynamics, influenced by charisma, popularity, loudest person at the table, self-doubt, fear of ridicule or rejection, you name it, it has the potential to suppress the good ideas, or to water them down so much that they cease to be good.

Think of the noise that surrounds an endeavour, elevating concerns that have no right to be considered alongside those that are absolutely essential, the group member with a hobby horse. No. None of that, a solid synthesis of prior art applied to a lucid definition of the problem.

One of the most important features of avoiding groupthink is not obsessing over local maxima; the tendency might be to think that having more members might widen the possible question or solution space, but often the reality is that there is a convergence of opinion on a local solution to a local problem that ignores the flash of inspiration coming out of left-field that can only be the result of deep reflection or unique experience and knowledge. Often the solution does come from this place because if it were discernible by other means then it would have been arrived at already.

So it was in the case of `Object.observe`. No amount of committee back and forth arrived at the jump necessary to obviate it completely with a better alternative which attacked and defeated the problem from a completely different angle and approach. A pure UI, the function of application state is so far removed from the local discussion of how to notify state change for keeping a UI up-to-date because it removes the problem. It comes from a different perspective of the issue, and when arrived at, the solution becomes self-evident.

—*Saturday 16th January 2021.*
