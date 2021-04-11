---
layout: post
title: Do The Work
---

This morning, during my daily Bible reading, I read the following verse:

> Do not be haughty, but associate with the lowly.

The alternative reading in this particular translation is as follows:

> Do not be haughty, but give yourselves to _humble tasks_.

The emphasis is my own. The key trait necessary here is humility. And humility leads to diligence. Diligence is careful and persistent work or effort. And specifically here, careful and persistent work or effort _in tasks that you feel are beneath you_.

In modern terms we may prefer the term conscientiousness, wishing to do one’s work or duty well and thoroughly. There are certainly nuances of difference: in the latter, conscience is an explicit  driver of the work; in the former diligence is something persistent, work applied over time. The book of Proverbs has much to say about diligence (and by extension conscientiousness).

> the hand of the diligent makes rich

> the hand of the diligent will rule

> the diligent man will get precious wealth

> the soul of the diligent is richly supplied

> the plans of the diligent lead surely to abundance

> seest thou a man diligent in his business? he shall stand before kings;

In many ways, although I naturally fail to always live up to this standard, diligence (and conscientiousness) is one of my deepest core values. 

Do the work.

I wouldn’t normally blog about my morning reading—although it is Sunday today—but I came across this post from [Jacob Kaplan-Moss][jkm], co-creator of Python’s Django framework, called [Embrace the Grind][etg]. In it, Mr. Kaplan-Moss extols the virtues of _doing the work_.

And not just any work, but the gruelling, tedious, unattractive work that we, as (spoilt) software engineers, are very quick to consider beneath us.

His example was drinking from the poisoned chalice of an unkempt, unmanaged, overgrown bug database in complete disarray. He rolled up his sleeves and dove right in. He did the work. He took one for the team. He brought order to chaos. And he did a lot of other people a favour.

> I did the work. I printed out all the issues - one page of paper for each issue. I read each page. I took over a huge room and started making piles on the floor. I wrote tags on sticky notes and stuck them to piles. I shuffled pages from one stack to another. I wrote ticket numbers on whiteboards in long columns; … I spent almost three weeks in that room, and emerged with every bug report reviewed, tagged, categorized, and prioritized.

I’m assuming that this happened relatively early in his career. I think this attitude of humble diligence and his rise to the top of his field (co-creator of one of the world’s must successful web frameworks, successful engineering leader at Heroku and 18F—part of the United States Government Technology Transformation Services—and others) is no coincidence.

He was diligent in his business, and got to stand, literally, before kings of our time, in government.

---

His story reminded me of a couple of incidents from my own experiences as a software developer. 

In my second job we were dealing with the legacy of a Rails app that was written from before Rails hit 1.0 and thus had seen a lot of framework churn and contained a lot of outdated patterns. 

It had also chosen to integrate the YUI JavaScript Library which was already dated and dictated a particular approach to writing JavaScript which none of us on the team felt was a good idea. We’d tip-toe nervously around the JavaScript parts of the codebase and touch as little as possible mortified by the fear of breaking everything with a stray semi-colon (it’s a fear that I don’t think I’ve yet recovered from).

But it was unworkable. It slowed things down. It meant features tool longer to build. (An example of [exponential complexity][ec] if you will.) And we all knew that sooner or later we’d have to rip it out and replace it with the new kid on the block, jQuery.

I grasped the nettle. I documented the behaviour of all of the JavaScript across the whole site. I discovered swathes of code that were never executed, no doubt left alone to avoid the house of cards falling down. I worked out what was executed, when, and why. And then I started taking it apart, piece by piece and replacing it with a framework that we all understood and could all work with.

I curried a lot of favour in the team with that move. We all felt more confident in the code. We could make changes easily without a monkey on our backs. 

A couple of months later I was asked to be team lead.

---

At ClubCollect, despite having had a number of more senior positions prior, I joined on the lowest rung as an individual contributor. On the factory floor.

ClubCollect had seen a lot of growth before my arrival and as often transpires in such cases a combination of speed to market together with an imperfect understanding of the problem being solved meant a few mothballs had been swept under the carpet.

Do that enough times and you start tripping over the wrinkles that start appearing. One of those areas was the dispatching of email messages. There was a mammoth `Message` class approaching a thousand lines of code. And it was populated by no less than seventeen methods that shared almost identical attribute lists.

Given that messaging is one of the core activities of ClubCollect’s software then it’s no secret that no-one wanted to touch this code for fear of messing up a significant portion of the thousands of messages that we send out every day.

You reach a point however, when you realise the pain you will have long-term will dwarf the short-term pain of sorting it out. So I put on the rubber gloves and embarked on the cleanup job. 

I made a spreadsheet with a column of all the message types we sent out, around fifty messages and thus rows in total, and a new column for each attribute that was passed into the message, which came to more than thirty. And I went through each message and the associated method(s) and worked out which parameters applied to which message, and what all their possible values were.

The subsequent refactoring supported by my painstaking research involved a record number of Vim splits on a very large monitor. And given that we had to support the rendering of historical messages also included a test suite that verified that, for each message, the new way of putting attributes together was identical to the past. This was a refactoring true to the definition—behaviour didn’t change. I did it all in incremental commits and pull requests, one message at a time. 

It wasn’t very pleasant. It wasn’t very glamorous. But it was necessary.

I did the work.

Six months later I received this message from a number of my colleagues:

> Hi Philip! Sorry for pinging you collectively out of the blue, but we believe that Erik has most probably sent you an offer for the post of CC’s VP of Engineering. We’d like to assure you that, given you are considering that offer at all, you have our full support in pursuing this position … We unanimously agree that your personality traits as well as technical and communication skills make you The Right Man in The Right Place for that task.

This remains to date, given my respect for the authors, the most overwhelming professional vote of confidence I have ever received. I’d chosen for the quiet life at ClubCollect—free from responsibility—in order to rediscover my enjoyment of programming. But I proved that certain personality traits, diligence being foremost among them, mean responsibility is never far from the door.

> the hand of the diligent will rule

Do the work.

—*Sunday 11th April 2021.*

[jkm]: https://jacobian.org
[etg]: https://jacobian.org/2021/apr/7/embrace-the-grind/
[ec]: https://www.crossingtheruby.com/2021/04/10/graphing-software-complexity.html
