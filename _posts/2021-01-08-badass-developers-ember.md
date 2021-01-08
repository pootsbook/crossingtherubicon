---
layout: post
title: "Badass Developers: Ember"
---

When you compare what you had to do then to get something up and running compared to how you might be recommended to build something today in Rails, with true separation of concerns, ActiveRecord with simple data objects and wrappers for business logic, service objects removing logic from skinny controllers, persenters and decorators for the view layer, you begin to understand that Mr. Heinemeier Hansson made a conscious choice to leave these things out for the purpose of keeping things simple and approachable. Anyone could build a Rails application. This focus on narrowing down the core of what Rails was can be attributed to his no-nonsense, business focused, get things done mentality. He was focused not on building a great framework but on helping people build a great product, and the bi-product was a great framework. 

Rails was extracted out of a living, breathing, profit generating project management application, Basecamp. Rails helped people be better at their compelling context, and even greater, gave people something so exhilirating that it gave them a reason to look for a compelling context just so they could use it. The mountain of books on my shelf was reduced to one, Agile Web Development with Rails. People were saying Rails was awesome, not because it ticked some technical requirements list but because it removed the friction and frustration of previous methods, granting them the opposite; what they would have spent two months building would take two weeks. Rails had given them superpowers. And that was because Rails was designed to help them build a product and to remove or abstract or automate everything that stood in the way of that goal.

Date libraries with helper functions like: `first_day_of_month`; `last_day_of_month`; form helpers with `options_for_select` hooking up foreign key associations out of the box, RJS (despite all those who maligned it) gave you AJAX possibilities you never dreamed you would be able to do yourself (and allowed you to do it in five minutes!). 

They were all in the service of the goal, the wider context, build a great SaaS ~~application~~ business.

Ember.js came out of the blocks preaching much the same message. Of convention over configuration, but wasn’t, and still isn’t in my opinion, geared towards the bigger context. The first indication is, that for the same functionality (a blog) the getting started video is twice the length of the Rails video, appearing almost ten years later. It might not be fair to say that it has no bigger context but it does lead me to believe that that context is different. Ember’s bigger context seems to be ‘better than the alternatives by decreasing developer pain’. Notice that the out-of-the-box productivity aids like form helpers and validation helpers sit firmly outside the box of the framework proper. Ember recognized that convention over configuration encompassed the ‘gluing’ of libraries together into a coherent whole, but it stopped there, neglecting to offer strong conventions in the form of helper functions that allowed developers to shove their MVPs out the door. CRUD? Build it yourself. Helper mapping foreign keys in a select? Build it yourself. The foundation is there, but you have to build the building yourself, Rails had prefabricated floors that you stacked one on top of the other.

You could say that the complexity that Ember abstracts is solving a purely developer context, “I dont want to wire this stuff up manually, and I want all this stuff for free” whereas Rails targeted a certain-minded developer who had a product or business context, “I need the tools to build my application.” Rails did not assume you wanted to write your own code.

The Rails API has also remained relatively stable. The changes, while sometimes frustrating, have often been ancillary, the core has remained the same: routes with REST-ful actions mapping onto MVC. How you build a Rails application has not changed very much in ten years. (The _advice_ on how to build applications _has_ changed considerably.) Contrast that with the churn prevalent in Ember.js; if you have been on board since the beginning you have practically been dealing with two or three different frameworks and it shows no signs of abating. The improvements to the developer experience are incremental but often outweighed by the cost of upgrading and updating. Deprecations have improved the situation immeasurably but you still have to do the work.

The churn also contributes to two problems: learning the framework is a moving target; having learned the framework does not guarantee you will always know it. When learning something there is always what Sierra calls ‘the gap of suck’ and getting out of it is essential in keeping people interested. What can stop people is ‘death by a thousand derailers’, in Ember, there is a lot to learn up front, what all the pieces are and how they fit together. To produce something useful you often need all the parts, it sets a high bar for getting something done. Hit a problem or an issue anywhere along that path (and the chances of that are greater when you have more to get through and there are more parts; at least _n_ problems and potentially 2<sup><em>n</em></sup> problems depending on how the parts interrelate) and you derail yourself. Rerail yourself a few times and you lose motivation, lose enough motivation and you stop. 

What can exacerbate this problem and make people feel like idiots is telling them it is easy. What helps is to admit it is hard, and help people get over the hump.

Now forcing or encouraging people to upgrade poses a different problem. Say they kept going, got out of the frustrating and difficult period to a level of proficiency. What an upgrade then does is open a trapdoor and plunge them back into that mire, destroying all sense of fulfillment and accomplishment, and putting people through the ‘beginner’ experience all over again.

Now I don’t say that to criticise but simply to tell it how it is, two of the top three commented threads on the Ember.js discussion forum are “[Why I’m leaving Ember][lejs]” and “[Is the six-week release cycle too frequent?][rejs]”. The top post “[How do we beat AngularJS in the developers’ mindset?][ajs]” contains myriad ‘derailers’. Ember.js is a great framework once you get to grips with it, but getting there can be a challenge.

When this kind of accusation is made the temptation is often to say, “the language you gave me is the source of these problems”. And you might be right. This is JavaScript after all, not Ruby.

If you care enough though, you might use (or design) a different language.

—*Friday 8th January 2021.*


[lejs]: https://discuss.emberjs.com/t/why-im-leaving-ember/6361/81
[rejs]: https://discuss.emberjs.com/t/is-the-six-week-release-cycle-too-frequent/7526
[ajs]: https://discuss.emberjs.com/t/how-do-we-beat-angularjs-in-the-developers-mindset/3948
