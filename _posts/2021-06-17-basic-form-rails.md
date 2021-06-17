---
layout: post
title: "Basic Form: Rails"
---

To represent our [basic form][bf] in Rails we need an ActiveModel object and a template. I’m foregoing persistence and leaving ActiveRecord out for now.

```
class EmailSubscription
  include ActiveModel::Model
  
  attr_accessor :email
  validates :email, 
    presence: true, 
    email: true
end
```

That last line `email: true` leans on the following custom validator:

```
class EmailValidator < ActiveModel::EachValidator
  def validate_each(record, attribute, value)
    unless value =~ /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/i
      record.errors.add attribute, (options[:message] || "is not an email")
    end
  end
end
```

In the controller we need to initialize a new object so that it can be used by the form in the template.

```
def new
  @form = EmailSubscription.new
end
```

And then the template is as follows:

```
<%= form_for @form, 
    as: :subscription, 
    url: email_subscriptions_path do |f| %>
  <div class="form-group">
    <%= f.label :email, "Email address" %>
    <%= f.email_field :email, 
        class: "form-control #{@form.errors[:email].any? ? "is-invalid" : nil}"
        'aria-describedby': "subscription_email_help" %>
    <% if @form.errors[:email].any? %>
      <%= content_tag(:div, @form.errors[:email].join("; "), class: "invalid-feedback") %>
    <% end %>
    <small 
        id="subscription_email_help" 
        class="form-text text-muted">
      We'll never share your email with anyone else.
    </small>
    <%= f.submit "Submit", class: "btn btn-primary" %>
  </div>
<% end %>
```

There are certainly some things we could do to clean it up. Perhaps extract some elements into a form builder, like the error class and message.

The help text is also something pretty standard, although optional, and has no in-built Rails convention. It is similar to the label in that the accessibility text is referenced creating an interrelationship between form-control and another element, something that is ripe for abstracting.

It feels then like a half-way house. Some things are provided in a particular fashion, and it’s up to you to do the rest. In the same vein, you are writing some of the HTML yourself while also leaning on HTML generating helpers.

—*Thursday 17th June 2021.*

[bf]: https://www.crossingtheruby.com/2021/06/16/form-formation-a-basic-form.html
