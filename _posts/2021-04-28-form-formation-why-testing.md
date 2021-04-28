---
layout: post
title: "Form Formation: Why? Testing"
---

Some of the [trade-offs][fto] mentioned yesterday deserve a little more detail as I seek to make a case as to why I’m interested in a more declarative way to build forms.

I’ll start with the last benefit I mentioned, automated testing.

One of the more generally accepted pieces of received wisdom in the Agile community is the [testing pyramid][tp]. The principle is roughly that testing should occur at varying degrees of granularity or scope; there should be more granular tests, which traditionally are less complex (the base of the pyramid), than tests with a wide scope, which are traditionally more complex (the top of the pyramid). The volume of the pyramid is analogous to the number of tests. The testing “ice-cream cone” is a different perspective on the same principles but the volume of the cone is the complexity of writing and/or running the test. (The [testing iceberg][ti] is yet another take.)

Leaving aside for now the discussion as to whether the traditional categories still hold given the advances in test automation and the introduction of more convenient APIs to drive headless browsers, we can settle on a general principle that will always hold: the easier something is to test, the more likely it is we will test it, and the more confidence we will have in the system as a result.

If we flip that on its head we can also say that making something easier to test provides benefits for the system.

And given the complexity of certain kinds of tests, we can apply some “judo” and chop the complexity by moving the burden of proof to an area that is easier to test.

Testing “at the right level” is a more nebulous concept that can also be subjective, but its essentially the same thing. We ask the question, given the relative complexity of tests, what is the least complex or costly method of testing required such that I maintain my confidence in the system? A Goldilocks approach to testing if you will; not too hot, not too cold, just right—but watch out for the bears.

A form that is declared imperatively in the HTML, or with HTML helpers, will need to be tested at the level of HTML. This is near the top of the pyramid and the ice-cream cone, meaning the complexity of doing this is high and the appetite is low (for tests, not for ice-cream). You’ll need to run some (headless) browser tests or you’ll have to invoke a framework’s rendering system, but either way you’ll be doing a lot of element selection and verification. And you’ll do it for each form that you have (if you want to test all your forms).

Given forms are a key location of interaction with and gathering data from users I like them to work. And one way to check that is to write tests. Automated tests. I have no desire to open a browser and start clicking around when the computer could do that for me.

But what if I, or the computer, didn’t have to click around. Or didn’t have to do it nearly as much.

What if you were able to test every form control once, and a form in its totality, once, and then be done with it?

That’s the promise of declarative forms. If you accept the trade-offs then you can make your life a lot simpler on this front.

Imagine you have twenty forms, and those forms have a combined twenty different kinds of form controls. If you were testing everything on a per form basis, you could have as many as four hundred different combinations. Actually, you could have far more than that, but let’s keep the mathematics simple.

If you were able to test that _a_ form worked, and that each control worked in isolation, and you relied on a declarative form definition that used this code, then you write around twenty-one tests. And then you’re done. With the HTML part at least.

You can always test the declaration of the form at a different, easier, deeper down the cone, lower down the pyramid, but still on top of the iceberg fashion. You’ve effectively taken a bunch of blocks from the top of the pyramid and cemented them lower down. You’ve successfully managed to push the ice-cream further down into the cone without the bottom falling off, and crucially, without getting ice-cream on your hands. And you’ve avoided a titanic accident because the business value of the tests remain above water.

I think this is something everyone can get on board with. And you always have a life-boat, for that one exceptional case, of breaking out of the straight-jacket (and into the life-jacket?) to do something unique in an imperative fashion if you really need it.

—*Wednesday 28th April 2021.*

[fto]: https://www.crossingtheruby.com/2021/04/27/form-formation-trade-offs.html
[tp]: https://martinfowler.com/articles/practical-test-pyramid.html
[ti]: http://claysnow.co.uk/the-testing-iceberg/
