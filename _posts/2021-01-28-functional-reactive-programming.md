---
layout: post
title: Functional Reactive Programming
---

<p>
  <small>
    <em>
      Penned on January 26th 2016
    </em>
  </small>
</p>

Functional Reactive Programming is not something you come across every day. It’s most likely to cause you to choke on your breakfast as you read over the morning paper. Split it up a little and it doesn’t quite present itself as such a menace and possibly gives it room to take a place in the tidy and well-ordered tool-shed of your brain.

Functional Programming is easier to stomach as the tide of programming paradigms shifts slowly but surely back towards a style that has always been standing around at the party without ever being its life and soul. The perception towards it is changing as we dig ourselves into deeper holes with the tools we have had up until this point.

Reactive Programming on the other hand is something altogether new, or so it seems. It is certainly worthy of the term ‘paradigm shift’ because it challenges and asks us to step away from the well worn path of our daily habits and routes, to tread a new path. The undergrowth will always obscure the way but a clearing of lucidity and usefulness awaits.

When we think, reactive, we often define it in contradistinction to passive, or even impassive. But programs are not passive, nor can they be, so we can rummage around a bit more to find pro-active. Programming is an active activity, we want the computer to do something and in a pro-active or imperative sense we tell the computer what to do, in what order, and rejoice as the instructions are followed.

This pro-active or imperative approach lends itself well to situations where there is a clear start or trigger to the actions to be carried out, a clear end, and nothing much of any consequence in between.

However, increasingly, our programs are dependent on asynchronous events and behaviour, and many events—or triggers for further computation—are happening all at the same time—or concurrently. We can ‘fake’ a reactive style by using the observer pattern, registering a set of callbacks to run when an event is triggered. This approach often balloons in complexity (the so-called “callback hell”) and offers us the opportunity to rethink how we approach the problem. And this is where reactive programming comes in.

When we talk about asynchrony or synchrony or events, what we are really talking about is _time_. And specifically, things (values) changing over time. In imperative programming we usually achieve this behaviour by capturing the change to these values indirectly via state and mutation of that state. In other words there is no first-class (top-level) way to program with changing values except to update those values imperatively when notified of an event that might change them.

What follows is that there is no historical record of that value (past, present, future), only the present value. Also the values, by virtue of being updated after events have been triggered, are discrete, they happen at a particular point in time. They are not continuous, because the imperative style is temporally discrete. It is therefore indirect and discrete. 

Reactive Programming is a way to model the world where we can create a better abstraction for handling things in our program that change over time. It is thus direct and continuous. We treat dynamic, evolving values (i.e. values over time) as first-class values. This allows them to be defined and combined, and passed in and out of functions, these are ‘behaviours’. 

Imperative style is an accommodation to the machine and not a natural description of behaviour itself. Behaviour is a function over (continuous) time.

—*Thursday 28th January 2021.*
