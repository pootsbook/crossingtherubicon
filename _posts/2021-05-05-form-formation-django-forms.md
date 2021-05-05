---
layout: post
title: "Form Formation: Django Forms"
---

When reading through [Bureaucrat][bc] it was mentioned that it took its inspiration from Django. After having had a brief look at the guides and API documentation for [Django forms][df] then it becomes clear that Bureaucrat is a fairly faithful port of Django’s Form class.

What’s interesting is that unlike all of the Ruby libraries I have come across, Bureaucrat excepted, Django makes a form a first class object in the framework. It models a form explicitly in a way that others don’t. It’s noteworthy because of its relative uniqueness. Why one language and framework ecosystem approaches forms in a markedly different manner to others is a question worth asking, but that’s not what I’m doing here. I simply want to point it out.

The rationale behind this approach is partially addressed in the forms guide:

> Handling forms is a complex business. Consider Django’s admin, where numerous items of data of several different types may need to be prepared for display in a form, rendered as HTML, edited using a convenient interface, returned to the server, validated and cleaned up, and then saved or passed on for further processing.

In short, given the complexity of all of the pieces that need to come together to effectively handle the collection and processing of data via HTML forms it is worth treating this as a primary problem.

> Django’s form functionality can simplify and automate vast portions of this work, and can also do it more securely than most programmers would be able to do it in code they wrote themselves.

Django’s goals with respect to forms seem very similar to those I have sketched out in previous posts, namely _simplicity_ and _automation_. 

> If you can guarantee that level of predictability, if you choose for a standard approach, you can reduce the complexity…

> It also opens up the door for a greater level of automation.

—[Form Formation: Trade-offs][fft]

Django also adds to those the issue of _security_. If code for handling forms is centralised then the burden of security falls on the central library code and not on the programmer‘s particular implementation.

It looks like looking into Django’s approach will prove fruitful. Indeed the major selling point of Django in its early competition with Rails for developer mindshare was an instant admin interface, helped in large part by the automated generation of forms for model objects.

—*Wednesday 5th May 2021.*

[bc]: https://www.crossingtheruby.com/2021/05/02/form-formation-bureaucrat.html
[df]: https://docs.djangoproject.com/en/3.2/ref/forms/api
[fft]: https://www.crossingtheruby.com/2021/04/27/form-formation-trade-offs.html
