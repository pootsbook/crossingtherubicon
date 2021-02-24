---
layout: post
title: Breadboard vs. Circuitboard
---

When writing about the [flexibility of a minimal library][fs] like Sew compared with the relative inflexibility of a broader framework like Jekyll I thought of the following analogy. Using a minimal tool is a lot like using a breadboard whereas a framework behaves much more like a printed circuit board.

A breadboard is a tool for prototyping electronics. It is solderless, that is, instead of soldering wires and components together you can plug them into the board. Since the components are not hard-wired it is easy to lift them out and adjust the configuration as needed.

A circuit board is by definition hard-wired, while it may have some ports and adapters, all of the wiring is baked into the board in a preset configuration. Should you wish to change something that isn’t designed for change up front then it is either impossible or you have to go well out of your way to fiddle with the board.

In principle, a breadboard will eventually lead to a circuit board. That is, the breadboard outlives its usefulness once the prototype has been proved and the experiment is successful. It is not practical to mass-produce.

This is, in many ways, the difference between hardware and software. Hardware is hard: pre-baked and hard-wired, difficult or even impossible to change. Software is (supposed to be) soft: malleable, soft-wired, and easy to change.

Applying our analogy to frameworks and smaller tools then frameworks can feel more like hardware than software. It is not easy to change something that wasn’t designed for change. While frameworks do have ports and adapters (for example, Jekyll’s configuration possibilities for a different markdown processor) which allow for some level of flexibility, the moment you want to change something unforeseen by the framework authors (for example, deep locale support) it becomes a real challenge.

This poses interesting questions; how do we keep software _soft_, without having to reinvent the wheel each time? Is it possible to have a breadboard with easily pluggable components that work well together? Can we make the ‘circuit board’ of a framework more flexible? I don’t have the answers, but I do know that I prefer my software soft. 

—*Wednesday 24th February 2021.*

[fs]: https://www.crossingtheruby.com/2021/02/23/sources-of-complexity.html
