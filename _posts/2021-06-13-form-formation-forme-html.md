---
layout: post
title: "Form Formation: Forme HTML"
---

In yesterday’s [brief overview of Forme][fe] we looked at the core classes `Forme::Tag` and `Forme::Input`. These are integrated into a `Forme::Form`.

To define a form, in this case without a backing object, you initialize a `Forme::Form`. The resulting object then exposes the rest of the functionality as methods on this object, similar to the Rails form builder:

```
f = Forme::Form.new
f.open(action: '/foo', method: :post)
f.input(:textarea, value: 'foo', name: 'bar')
f.input(:text, value: 'foo', name: 'baz')
f.button('Create')
f.close
```

This would result in the following HTML, each method corresponding to one line of HTML:

```
<form action="/foo" method="post">
  <textarea name="bar">foo</textarea>
  <input type="text" name="baz" value="foo" />
</form>
```

There’s not really anything special about this other than a means to make the construction of HTML a little more straightforward. There’s a higher level DSL too:

```
Forme.form(action: '/foo', method: :post) do |f|
  f.input(:textarea, value: 'foo', name: 'bar')
  f.input(:text, value: 'foo', name: 'baz')
  f.button('Create')
end
```

The `Forme.form` method is fairly flexible and accepts several different argument signatures, each offering a nuance in the form’s construction.

It becomes more interesting when an object is passed in. Forme is designed to take advantage of this object in the construction and configuration of the form. This looks like the place where most of the benefits for customisation and automation can be realised.

Forme offers a way for form (model) objects to integrate with its API. There is an in-built plug-in for Sequel, which is not surprising given both libraries’ common author.

It is quite comprehensive and leans on Sequel, introspecting the model object and the object’s related attributes, schema, and validations to define all of the lower level Forme configuration.

It is very reminiscent of Rails as it boils a form down to a model object and a set of inputs which draw all of their configuration from the model object. Forme with the Sequel plug-in automatically detects the type of input based on the type of the database column, and also derives labels and errors.

I haven’t had time to dive into how precisely it goes about doing this, but in principle it should be flexible enough to be integrated with any ORM or even an non-database backed object given an implementation that respects Forme’s interface.

It means that validations, and the specification of a form object both lie firmly outside of Forme’s domain. It feels like a library designed for heavy duty convention/automation on the basis of these parts being present elsewhere. Forme does all of the heavy lifting for providing a configurable API for the generation of HTML. It is up to you to integrate it for your particular use-case.

—*Sunday 13th June 2021.*

[fe]: https://www.crossingtheruby.com/2021/06/12/form-formation-forme.html
