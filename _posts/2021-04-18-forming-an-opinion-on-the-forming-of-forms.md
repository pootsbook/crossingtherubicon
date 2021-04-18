---
layout: post
title: "Forming an Opinion on the Formation of Forms"
---

At the end of [yesterday’s post][yp], having explored a successful route to integrate the `select` with `option`s into a form data structure, I was filled with ideas of potential APIs to make the definition of this object more concise.

> There are a few routes there too: like creating a new “form” object wrapping the Scrivener object and delegating when necessary; or working ‘above’ the Scrivener level and dynamically generating the Scrivener object as a by-product of a higher level data definition which includes both attributes and their desired representation in a form; and there may yet be others.

It’s probably too early to dive into different details, however, I think it is good to make clear that this is very much an exploration of the space around defining forms in HTML that are connected in some way to a validation or model object. Rather than immediately jumping up to look at ways of implementing this in other frameworks and languages I’d like to feel out the edges of the problem from the centre. Naturally, this will fall short in some aspects. For example, after yesterday’s post a friend (and reader) wrote in:

> You probably don't want to start off having your data stored as options from the control. Take for example a hotel is checking in three different people and you need a nationality select for each person. You don’t really want to be passing that data around 3 times. Ideally your initial model should have the data for the field but the options should be a reference. Which then can be iterated for each field that references it. 

He is absolutely right. There are, at this early stage, going to be things that don’t add up. Or things that could be done better or be more faithfully. I think this is why (it feels like) this is such a thorny problem and why I’m fascinated by it. I’m not convinced by how we build forms in the frameworks that I have used (Ruby on Rails and Elixir’s Phoenix). Obviously, my ignorance could be the contributor here, sometimes you don’t realise that a solution you’re not happy with comes about because you don’t understand the problem well enough to accept that there are no perfect solutions. That the trade-offs are costly. Maybe I’ll find that out too. But there’s value in finding and understanding the trade-offs.

This presents a difficult challenge. We often implicitly accept that trade-offs were too large—we take a solution to a problem we don’t fully understand on blind faith. We trust that the framework author, having made so many right decisions in other areas, made the right decision here too. Is it then worth diving in to turn blind faith into reasoned faith? For me it is. It’s nothing more than a gut feel, a desire for something better. And “better” is so intangible, what does that even mean? 

To give you an idea I’ll share something I sketched out this morning that flowed out of the thoughts provoked by yesterday’s post. It’s not _the_ solution, but it’s an illustration of one member of a set of potential solutions. And it is also to provoke you. What do you think something like this should look like? Are we right to define our markup in our templates with our validation logic in our application? Should these be coupled or de-coupled? Integrated or disintegrated?

I’ll leave this here as a prompt, without comment:

```
class RegistrationForm < Formation
  field :name, tag: :input, type: :text      # , label: "Your name"
  field :email, tag: :input, type: :email    # , label: "Contact email"
  field :message, tag: :textarea             # , label: "What’s your bag?"
  field :enquiry,
    tag: select,
    label: "Would you like to join our SPAM list?",
    options: [{"Yes" => "1"}, {"No" => "1"}]
  field :submission, tag: :input, type: :submit, value: "Send"
end
```

—*Sunday 18th April 2021.*

[yp]: https://www.crossingtheruby.com/2021/04/17/form-representation-as-data-3.html
