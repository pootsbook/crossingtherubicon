---
layout: post
title: "Form Formation: Constituent Parts"
---

I received a bit of encouragement from [Frank Chimero][fc] today. I had been reading his blog post from 2018 about the disorientation caused by the shifting foundation of building for the web in [Everything Easy is Hard Again][eeha]. I’d like to write more about his observations which are very much in line with my thinking in this blog with respect to building on top of the right primitives.

He is very much focused on the front end, CSS and JavaScript, but I think his observations apply more broadly. There was something specific which answered to the question I posed in [yesterday’s post][yp] about whether or not it was worth exploring a “solved problem”. He writes:

>  the call for legibility should also humbly apply to writing legible code and designs [sic] systems that are easy for nearly anyone to interpret thanks to their elegance. That important work has a place, too.

This is what we’re looking for. Simple design. There are other great points but it’s important to have this goal in mind when we approach problems.

Now one of the reasons forms are so complex is because there are a number of things going on, and they’re happening at different angles, and they have to meet together in the right way. The simplest option is Do It Yourself. That is, leave the HTML generation to be hand-crafted in every instance. A step-up, but in the same tranche, is to offer tools to ease the HTML generation with helper methods, taking away some of the boiler plate.

HTML is ripe for styling. And the specific HTML that a form must take is dependent on those styles. Think of popular UI frameworks like Bootstrap, Tailwind, Bulma and the like. These frameworks often dictate the particular arrangement of HTML to get the form just so. This is an added complexity.

And then of course there are the “semantics” of the form. That is, a form is normally a (partial) representation of some or other object in a data store (or just in memory) that we are wanting to create or manipulate in some way.

And there is the life cycle of the form. The form has a life cycle. The easiest of forms you submit and the data flies off and you’re done. But most likely the data will come back with some meta-data attached, and this too will need to be displayed. Think of field errors displayed above or alongside the field itself within the form. So a form must maintain a certain state across a stateless request cycle, and that state is transformed.

The straight division between the HTML and styling on one side and the object and life cycle on the other is most common. Why would anyone want to do different? That’s a question for another day.

—*Monday 19th April 2021.*

[fc]: https://frankchimero.com
[eeha]: https://frankchimero.com/blog/2018/everything-easy/
[yp]: https://www.crossingtheruby.com/2021/04/18/forming-an-opinion-on-the-forming-of-forms.html
