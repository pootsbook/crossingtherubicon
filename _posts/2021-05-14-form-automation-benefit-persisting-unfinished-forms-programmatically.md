---
layout: post
title: "Form Automation Benefit: Persisting Unfinished Forms Programmatically"
---

When looking at [Sanity Schemas][ss] and [Sanity UI][sui] it is clear to see the benefits of abstraction and automation in form construction. From a simple set of schemas you can generate a complete content management system.

At work today I spotted a colleague’s Pull Request that displayed another benefit of abstraction and automation, or at least the promise of such.

First, a pet peeve. One of the most annoying things on server-side web applications (and not all client-side, “single page” applications protect against this either) is to have put a lot of time and effort into filling out a long form only to see your session expire when you try to submit it, losing everything as a result. You are greeted with a login page and even after signing in a wistful click of the back button presents you with a cold, empty form, your effort gone to waste.

Despite the depth of suffering that I have endured from these scenarios in the past I must admit that in our own application at ClubCollect we are guilty of not covering our users’ backs in this respect.

Shame on us. The solution is relatively straightforward. This is a [tiny win][tw]. Props to my colleague for sympathising with the latest poor user who was not so disenchanted (or perhaps because he _was_) that he wrote in to tell us of his ordeal. The stock response would be, “sorry, you should have been logged in”. But that’s not how we do things at ClubCollect. At least not today.

The fix is around forty-nine lines of code, and that’s forgiveable because most of it is in JavaScript, a language that cares not for concision.

At ClubCollect we use Basecamp’s [Stimulus][stim] framework as we write the least possible amount of front-end JavaScript that we can get away with.

This is the Stimulus controller which can be bound to a form-control, in this particular case, a `textarea`. 

```
window.register_stimulus_controller("text-saver", {
  statics: {
    targets: ['input']
  },

  connect: function() {
    for (var i = 0; i < this.inputTargets.length; i++) {
      var input = this.inputTargets[i];
      this.load(input);
    }
  },

  changed: function(event) {
    this.save(event.target);
  },

  submit: function(event) {
    this.clear();
  },

  load: function (input) {
    var key = $(input).data("text-saver-key");
    var value = localStorage.getItem(key);
    if (value) input.value = value;
  },

  save: function (input) {
    var key = $(input).data("text-saver-key");
    localStorage.setItem(key, input.value);
  },

  clear: function () {
    for (var i = 0; i < this.inputTargets.length; i++) {
      var input = this.inputTargets[i];
      var key = $(input).data("text-saver-key");
      localStorage.removeItem(key);
    }
  },
});
```

This controller captures the value of its target as it changes and persists it to local storage. If you load the page then it looks for any value stored for that particular form and field in locale storage and populates the value of the `textarea` if found. When you successfully submit a form then it clears the corresponding entry in local storage so that you can start afresh.

This is how it is hooked up to the Rails form helper, with everything configured via `data-` attributes as per Stimulus:

```
<%= form_tag(member_invoice_tickets_path(@invoice),
      'data-controller': 'text-saver',
      'data-action': 'submit->text-saver#submit') do %>
  …
  <%= text_area_tag(
        'question',
        nil,
        class: 'form-control',
        rows: '3',
        'data-target': 'text-saver.input',
        'data-text-saver-key': "member-invoice-ticket-reply-#{@invoice.id}",
        'data-action': 'keyup->text-saver#changed') %>
  …
<% end %>
```

Now this is implemented on one form and one text area within it. If we wanted to apply this pattern across all text areas on the site then, using the imperative route, we’d have to repeat these boilerplate data attributes each time. This is despite the value of `data-text-saver-key` being the only thing that is effectively different between each instance of the form control.

We could roll it into a wrapper for the `text_area_tag` / `text_area` helpers. This would give us access to the form object, the Rails model, so that we could generate the corresponding unique key. That’s one way.

Another way, using a declarative approach, is to build this into the standard form definition and associated widget. We write it once and use it everywhere. 

This is the power of abstraction. It allows us to hook in at a deeper level. Rather than editing each instance of a form and text area we edit the library that generates our forms and we automatically roll out this “tiny win” across the whole site.

Who doesn’t want that?

—*Friday 14th May 2021.*

[ss]: https://www.crossingtheruby.com/2021/05/12/form-formation-sanity-document-schemas.html
[sui]: https://www.crossingtheruby.com/2021/05/13/form-formation-sanity-ui.html
[tw]: http://joelcalifa.com/blog/tiny-wins/
[stim]: https://stimulus.hotwire.dev
