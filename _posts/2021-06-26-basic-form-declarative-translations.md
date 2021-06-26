---
layout: post
title: "Basic Form: Declarative Translations"
---

In yesterday’s post about [Simple Form][sf] I noted that it was possible to declare the UI text via i18n ‘translations’.

This is an interesting counter-point to the otherwise imperative nature of constructing forms in Rails, whether it be with the default form builder or a library like Formtastic or Simple Form.

That is, the UI text placed in an i18n translations file is a declarative way of specifying the text, the attribute of the form to which the text should be linked, and the specific element of the form control for each bit of text.

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

This is nothing other than a declarative description of the form text, and if you squint, of the form itself. I find the mix of imperative (template) and declarative (translations) ways of specifying aspects of the form a curious mix.

—*Saturday 26th June 2021.*

[sf]: https://www.crossingtheruby.com/2021/06/25/basic-form-simple_form.html
