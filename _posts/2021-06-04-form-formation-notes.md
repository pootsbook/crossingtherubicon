---
layout: post
title: "Notes about Form Formation"
---

_I received an email from a reader with some notes to complement my recent [reflections][ref] on form formation. They are a lucid articulation of some of the themes I have been investigating. With [Michel Martens][mm] generous permission I am publishing his notes on this blog as a guest post._

Let’s say a *form* is an HTML artifact. It may or may not have a server-side counterpart, which I will call a *filter* for lack of a better word. A filter is a set of constraints and transformations applicable to data coming from the form. It may include other business logic and it may be connected to zero, one, or many models. A *model* represents a coherent subset of all the relations that exist in the application, along with a set of pertinent operations for retrieving, storing, presenting, and modifying data. Finally, the model may be connected to a *datasource* and a *datastore*, although most of the time a database plays both roles.

When data moves from the datasource to the model, then to the filter, then to the form, it gets decorated. When it moves in the opposite direction it gets refined. The form displays the data coming from the datasource but it also displays hints, labels, placeholders, constraints, and validation errors. The way a form looks is independent from the data it displays. That means the system described—datasource/datastore, model, filter, form—contains instructions that range from those related to data integrity to those related to presentation. Part of the problem with form formation has to do with how and where to encode those instructions; another part of the problem has to do with whether or not to encode them.

In the case of Django Forms and Bureaucrat, the instructions for how to generate the HTML are encoded in the filter. Customisation is possible by extending or modifying the filter. In the case of Rails, the filter and model roles are conflated. Rails helpers, Formtastic, Simple Form and similar tools provide functions for deriving the HTML from the information encoded in the filter/model complex. Customisation is possible by modifying the generated HTML. Both approaches can lead to anti-patterns—code smells such as Divergent Change, Shotgun Surgery, and maybe others.

The best opportunity for automation arises when a single model is tied to a single filter and a single form. If the look and feel of the form is not important, then everything can be derived and it does not matter where the directives are encoded.

—*Friday 4th June 2021.*

[ref]: https://www.crossingtheruby.com/2021/06/01/form-formation-reflections.html
[mm]: https://soveran.com/about.html
