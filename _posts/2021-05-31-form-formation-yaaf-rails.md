---
layout: post
title: "Form Formation: YAAF (Rails)"
---

[YAAF][yaaf] is a form library for Rails similar to [Reform][rf]. It stands for “Yet Another Active Form” and allows you to create form objects in a manner similar to the “Rails way”.

As such it is more tightly coupled to ActiveModel and ActiveRecord in a way that Reform is not. YAAF is a Rails-only library. Other frameworks? YAGNI.

YAAF is agnostic about the rendering of your form. It provides the same API to the view so that you can continue as normal, either using the default `form_for` or the methods provided by Formtastic and Simple Form.

It is then a library that concentrates on the “form object” abstraction, creating a bucket for your form to live separate from the rendering of the form and the model(s) that back it.

The motivation is to provide form objects that behave like Rails’ models, that the library is simple to use and understand, easy to customise and well tested.

YAAF is providing a little bit of sugar to make the Active::Model form object approach cleaner. A YAAF form object is designed to be interchangeable with an ActiveModel object but rather than manually specify all the machinery for a use-case like multiple models it handles it for you out of the box.

And the reasons for using YAAF are much the same as I observed with Reform. It keeps models, views, and controllers ‘thin’ by allowing you to put form related (business) logic in a dedicated bucket. It decouples the one-to-one model-form mapping. It supports nested models, collections of models, and associated models. And one I hadn’t mentioned before but is a prominent benefit of YAAF, it allows the form logic to be front-end agnostic; an API or an HTML form can both use a YAAF form object as a basis.

What follows is a basic YAAF form designed for a registration form backed by a `User` model:

```
class RegistrationForm < YAAF::Form
  attr_accessor :user_attributes
  
  def initialize(attributes)
    super(attributes)
    @models = [user]
  end
  
  def user
    @user ||= User.new(user_attributes)
  end
end
```

In a controller you can replace the conventional Rails model with this form and use as normal:

```
class RegistrationsController
  def create
    registration_form = RegistrationForm.new(user_attributes: user_params)
    
    if registration_form.save
      redirect_to registration_form.user
    else
      render :new
    end
  end
end
```

YAAF certainly looks like an improvement to default Rails forms when tackling more advanced or complex scenarios like multiple models, nested models or form for a subset of a given model’s attributes.

It is nigh on invisible in the sense that it implements all of the standard Rails life cycle methods as if YAAF weren’t there.

For developers that are bought into the Rails way then this offers just enough to convince you that it brings added value without the need to learn a new abstraction or API.

What’s impressive about YAAF is that it provides all of its functionality in under sixty lines of code (47 SLOC), preferring to lean heavily on code already existing in Rails itself. This has the benefit of little additional complexity and also offering APIs and concepts that will be familiar to those working in Rails.

—*Monday 31st May 2021.*

[yaaf]: https://github.com/rootstrap/yaaf
[rf]: https://www.crossingtheruby.com/2021/05/29/form-formation-reform.html
