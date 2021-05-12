---
layout: post
title: "Form Formation: Reflection on Rails Form Builder"
---

Yesterday I took a deeper dive into how Rails helps you build forms with a [form builder][rfb] following the builder pattern.

I always knew how this worked but after having looked at it in more detail, and with the context of a desire to build forms in a different manner, it occured to me that the Django/Bureaucrat approach and the Rails approach are not that different.

And the key difference is not necessarily the location of the HTML generation, even thought that is different, but the key difference is rather the location of the mapping from an attribute on the “form object” (in Rails an ActiveRecord object, in Django/Bureaucrat the Form object) to a particular form control (a text input, or a text area).

Everything else is just noise. Of course it’s necessary, but HTML is HTML.

In Rails, the mapping between attribute and form control is imperatively defined in the form helper as part of your HTML (ERB) template.

In Django/Bureaucrat, the mapping between attribute and form control is declaratively defined in the form object.

Observed through this lens then there is actually not much different, conceptually, between the two approaches.

I look at frameworks as tools that advise you where best to put your code on the basis of their pre-defined buckets. Rails is saying, put your form definition in the _template_ bucket. Django is saying, put your form definition in the _form_ bucket. 

Rails has many buckets, but Rails doesn’t have a form bucket.<sup>1</sup>

—*Tuesday 11th May 2021.*

[rfb]: https://www.crossingtheruby.com/2021/05/10/form-formation-rails-form-builder.html

<ol class="footnotes">
  <li>
    Django pours the contents of the form bucket through a widget sieve into the HTML template bucket.
  </li>
</ol>
