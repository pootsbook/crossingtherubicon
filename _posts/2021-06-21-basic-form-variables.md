---
layout: post
title: "Basic Form: Variables"
---

What if we start from scratch and try and lift out all the variable aspects of a basic form?

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

We have:

- the form `action` attribute
- the form `method` attribute
- the field’s label `for` attribute
- the field’s label text
- the field’s control (`input` of type `email`)
- the field’s object and attribute `name`
- the field’s value
- an optional CSS class dependent on validation status
- the field’s id (object and attribute names)
- accessibility reference to the field’s help text element
- the field’s error message(s)
- the field’s help text
- the button text

Tomorrow we can divide these up into things that have to be provided and things that could be supplied or auto-generated from what is supplied.

—*Monday 21st June 2021.*
