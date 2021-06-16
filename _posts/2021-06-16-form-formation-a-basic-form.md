---
layout: post
title: "Form Formation: A Basic Form"
---

It’s time to get more concrete. 

It makes sense to start with an arbitrary standard for the HTML that acts representatively. For that we’ll use Bootstrap 4.6 as it has a developed system surrounding form controls.

For the form itself we’ll have an `action` of `/newsletter_subscription` with a `method` of `POST`. The form will contain one field, an email input, with a label and help/hint text alongside one submit button.

This is what the final HTML for the intial form should look like:

```
<form action="/newsletter_subscription" method="post">
  <div class="form-group">
    <label for="subscription_email">
      Email address
    </label>
    <input 
        type="email" 
        name="subscription[email]" 
        class="form-control" 
        id="subscription_email" 
        aria-describedby="subscription_email_help">
    <small 
        id="subscription_email_help" 
        class="form-text text-muted">
      We'll never share your email with anyone else.
    </small>
  </div>
  <button type="submit" class="btn btn-primary">
    Submit
  </button>
</form>
```

A form with an error should have the `is-invalid` class added to the `input` with an adjacent `div` with a class of `invalid-feedback` immediately succeeding the `input`. The full invalid HTML should look like:

```
<form action="/newsletter_subscription" method="post">
  <div class="form-group">
    <label for="subscription_email">
      Email address
    </label>
    <input 
        type="email" 
        name="subscription[email]" 
        class="form-control is-invalid" 
        id="subscription_email" 
        aria-describedby="subscription_email_help">
    <div class="invalid-feedback">
      Please provide a valid email address.
    </div>
    <small 
        id="subscription_email_help" 
        class="form-text text-muted">
      We'll never share your email with anyone else.
    </small>
  </div>
  <button type="submit" class="btn btn-primary">
    Submit
  </button>
</form>
```

That’s the starting block. The race entails passing the baton from each framework/library to the other to see how to bring it about. And then we can compare notes.

—*Wednesday 16th June 2021.*
