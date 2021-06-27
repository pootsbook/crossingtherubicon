---
layout: post
title: "Basic Form: Field Declaration"
---

The i18n specification of a form’s UI text in Rails (with Simple Form and Formtastic) presented a contradiction of sorts in form creation. I18n is declarative while the template promotes an imperative approach.

What if we could use the i18n file as a starting point to attempt a declaration of our basic form’s only field?

Here is our starting point, the i18n file that Simple Form uses:

```
en:
  simple_form:
    labels:
      subscription:
        email: "Email address"
    hints:
      subscription:
        email: "We'll never share your email with anyone else."
  helpers:
    submit:
      subscription:
        create: "Submit"
        edit: "Submit"
```

We see that each element is specified by its category and only latterly by its field. Let’s swap that around for readability.

```
en:
  simple_form:
    subscription:
      email: 
        label: "Email address"
        hint: "We'll never share your email with anyone else."
```

If truth be told, it’s starting to look a lot like the [Formtastic][ft] field declaration:

```
<%= f.input :email, 
      label: "Email address",
      hint: "We'll never share your email with anyone else." %>
```

In _Rails as She is Spoke_ Giles Bowkett notes how certain Rails methods which carry a basket of options resembles a declarative style more often associated with functional programming. This is certainly the case here. Let’s go a little further by specifying the type of form control.

```
en:
  simple_form:
    subscription:
      email: 
        control: "email"
        label: "Email address"
        hint: "We'll never share your email with anyone else."
```

Somewhere we could declare a mapping of `email` to an `input type="email"`. 

While this looks like a path forward it leaves validations and errors out in the cold. Validations need to live in the code, especially if we want to construct custom validations. If validations were standard they could be referenced declaratively like this:

```
en:
  simple_form:
    subscription:
      email: 
        control: "email"
        label: "Email address"
        hint: "We'll never share your email with anyone else."
        validations:
          unique: "must be unique"
          email: "must be a valid email address"
```

One option, although I don’t like how it splits a single concern into two pieces, would be to allow the key in the validations hash above to reference a method name.

Food for thought, and a step towards a potential declarative API for a form field. Or simply a conclusion that the APIs provided by Simple Form and Formtastic are, at least on the surface, declarative enough.

—*Sunday 27th June 2021.*

[ft]: https://www.crossingtheruby.com/2021/06/24/basic-form-formtastic.html
