---
layout: post
title: "Form Formation: Reform (2)"
---

Yesterday I gave a high-level introduction to Trailblazer’s [Reform][rf]. In the about page of the [Trailblazer book][tb], _Trailblazer, A New Architecture For Rails_, it describes the importance of forms (and Reform) and their responsibilities:

> Forms are an integral part of Trailblazer. They orchestrate deserialization of input, population and validation.

Forms are also mentioned as a key element in the Trailblazer architectural style. They can be paired with Trailblazer Operations for maximum effect.

But what does a form look like? As mentioned it is a distinct entity and defining a form class, the basis for a form object, looks like this:

```
class AlbumForm < Reform::Form
  property :title
  validates :title, presence: true
end
```

A form field is defined using `property`. Validations are added to fields using `validates`. The creation of a form object is done in normal Ruby style and requires a model which the form represents:

```
form = AlbumForm.new(Album.new)
```

To validate a form you can call the `validate` method passing the input data as the parameter:

```
form.validate(params)
```

The model remains untouched. The input data is the property of the form at this stage. If `#validate` returns `false` then you can fetch errors:

```
form.errors
```

These are returned in the Rails’ ActiveModel format. Should `#validate` return true, and you want to modify the model object, then you can call `#sync` which updates the object without persisting or `#save` which syncs and saves in one go. 

To render a form you can use the normal view methods but instead of passing a model object you pass the Reform form object:

```
form_for @form           # Rails default
semantic_form_for @form  # Formtastic
simple_form_for @form    # Simple Form
```

Where Reform really comes into its own is when there are more complex requirements that go beyond a simple one-to-one mapping between a form and a model object. That’s an avenue for future enquiry.

—*Sunday 30th May 2021.*

[rf]: https://www.crossingtheruby.com/2021/05/29/form-formation-reform.html
[tb]: https://leanpub.com/trailblazer
