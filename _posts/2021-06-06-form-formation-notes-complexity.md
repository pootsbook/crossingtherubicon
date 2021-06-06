---
layout: post
title: "Form Formation: Notes—Complexity"
---

In his [notes][mmn], Michel observed the responsibilities at play in the forms ‘system’ are mixed:

> That means the system described—datasource/datastore, model, filter, form—contains instructions that range from those related to data integrity to those related to presentation.

This is complexity in the truest sense, different strands intertwined with each other. I think a key element in this coming together is the circularity of the enterprise. That is, a form can be viewed as a loop. The data must be captured in an HTML form on the client and then sent to the server. Should the data be deficient in any way (e.g. a validation failure) then we have to decorate the object with feedback (e.g. a validation error message) that the server returns for display on the client in the ‘original’ HTML form.

Without this circularity then we’d simply have to rely on a simple contract with regards to the data structure provided from the HTML form and the data structure on the server. The circularity is complicated because every bit of information that decorates the form object on the server must have a corresponding presentation element on the client.

I wonder if my reservations about current approaches are based in the fact that these corresponding elements are dealt with in one manner while more incidental concerns in another. And my taste suggests that a much cleaner approach would be to be consistent in what Mr. Martens calls the “encoding” of the instructions. Although that also begs his question of whether it should be encoded at all.

—*Sunday 6th June 2021.*

[mmn]: https://www.crossingtheruby.com/2021/06/04/form-formation-notes.html
