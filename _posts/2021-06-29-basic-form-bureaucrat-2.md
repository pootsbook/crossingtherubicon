---
layout: post
title: "Basic Form: Bureaucrat (2)"
---

A closer look at a Bureaucrat `Field` shows that both labels and hints are configurable and can be provided on initialization. It means we can change our field definition:

```
require 'bureaucrat'

class SubscriptionForm < Bureaucrat::Forms::Form
  include Bureaucrat::Fields
  
  field :email, EmailField.new(
    required: true,
    label: "Email Address",
    help_text: "We'll never share your email with anyone else.")
end
```

The interesting thing to note is its resemblance to the other helpers we have used from Formtastic and Simple Form. The only difference is that instead of putting it in the template, it is configured in the Bureaucrat form class.

What remains a puzzle is why the default widget for that field does not include the label or help text in the output.

```
>> form = SubscriptionForm.new
>> puts form[:email].to_s
<input type="text" name="email" id="id_email" />
```

We can confirm that all of the data lives on the form object:

```
>> form[:email].label
=> "Email Address"
>> form[:email].help_text
=> "We'll never share your email with anyone else."
```

So we can think about defining a custom widget given that all of the necessary elements are present in the field itself.

—*Tuesday 29th June 2021.*
