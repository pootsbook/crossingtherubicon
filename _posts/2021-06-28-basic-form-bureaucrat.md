---
layout: post
title: "Basic Form: Bureaucrat"
---

Bureaucrat is a more declarative form library in Ruby. To define our basic form in Bureaucrat we would write the following code:

```
require 'bureaucrat'
require 'bureaucrat/quickfields'

class SubscriptionForm < Bureaucrat::Forms::Form
  include Bureaucrat::QuickFields
  
  email :email, required: true
end
```

An alternative definition without the `QuickFields`:

```
require 'bureaucrat'

class SubscriptionForm < Bureaucrat::Forms::Form
  include Bureaucrat::Fields
  
  field :email, EmailField.new(required: true)
end
```

An `EmailField` comes with an email validation included by default, so there is no need to specify it. 

All fields descend from one superclass, the `Bureaucrat::Fields::Field`. It uses inheritance liberally to define the different field types. The email field is a descendent of the `CharField` which is itself a descendent of the `Field` class.

The rendering of a form control is done with widgets. Each field has a default widget which in the case of our `EmailField` is an input. The default widget is actually quite bare:

```
>> form = SubscriptionForm.new
>> puts form[:email].to_s
=> <input type="text" name="email" id="id_email" />
```

This is a little disappointing. While Bureaucrat provides all of the individual pieces (form/model object creation, field definition, validations) it doesn’t offer an integrated way to pull them all together as declaratively as I would have liked. The configuration and display of a label, hint, and error messages is outside the scope of the default widgets.

I wonder if it would be possible to build on Bureaucrat with a custom widget producing the desired Bootstrap HTML.

—*Monday 28th June 2021.*
