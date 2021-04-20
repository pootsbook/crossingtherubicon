---
layout: post
title: "Form Formation: HTML Form Styling"
---

In yesterday’s post about the [constituent parts][cpf] of an HTML form, one of those parts was the styling of a form’s controls, written with HTML and styled with CSS. In this post I wanted to share some examples of how some popular CSS frameworks style form elements to get a flavour of what is involved.

Of course we could opt not to style a form and just serve up the raw HTML, but it is not unreasonable to expect that a library for generating forms should cater to styling needs and that it should be flexible enough to deal with different use-cases.

In the future we can look at how different form libraries behave but I can say up front that most of them leave all of the HTML generation to the end-user, opting out of that level of flexibility (and likely complexity).

I’ll take some arbitrary examples of form controls from Bulma, Bootstrap and Tailwind.

## Bulma (v0.9.2)

The first is my favourite of the three, if indeed one can have a favourite CSS framework at all. It offers a set of [generic form controls][bufc] as part of its package. A text input:

```
<div class="field">
  <label class="label">Name</label>
  <div class="control">
    <input class="input" type="text" placeholder="Text input">
  </div>
</div>
```

There is a wrapping `div` for the full “field” (label and control), a `label`, and an HTML `input` inside a nested wrapping `div`. Each HTML element, including the two wrapping `div`s has one class for styling.

Most of the other controls follow the same pattern with some minor variations. For example, a `select` has an extra wrapping `div`; `radio` and `checkbox` `input` types are wrapped within the `label`. `span`s can be used for prepending or appending icons to `input`s and `select`s; `p` can be used for adding help text, error, or success messages. A fuller example of an email input:

```
<div class="field">
  <label class="label">Email</label>
  <div class="control has-icons-left has-icons-right">
    <input class="input is-danger" type="email" placeholder="Email input" value="hello@">
    <span class="icon is-small is-left">
      <i class="fas fa-envelope"></i>
    </span>
    <span class="icon is-small is-right">
      <i class="fas fa-exclamation-triangle"></i>
    </span>
  </div>
  <p class="help is-danger">This email is invalid</p>
</div>
```

Horizontal forms bring with them their own particular flavour of HTML wrapping and associated classes, but you get the general idea.

## Bootstrap (v5.0.0-beta3)

Perhaps the most venerable of all, Bootstrap is the beast that gave birth to the profusion of CSS frameworks and, while of questionable worth, developers who design. [Bootstrap Forms][bofc] in this version seem to be simplified considerably. An email input:

```
<div class="mb-3">
  <label for="exampleInputEmail1" class="form-label">
    Email address
  </label>
  <input type="email" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp">
  <div id="emailHelp" class="form-text">
    We'll never share your email with anyone else.
  </div>
</div>
```

It has a wrapping `div` for the “field” and the rest is as raw as it comes in terms of HTML structure. No further wrapping and a simple `div` for help text. The form control elements `label` and `input` as well as the help text `div`all have classes for styling.

For “input groups”, which involve prepending and appending other elements to an `input`, a wrapping `div` is introduced for the “control”. 

```
<div class="mb-3">
  <label for="exampleInputEmail1" class="form-label">
    Email address
  </label>
  <div class="input-group mb-3">
    <input type="text" class="form-control" placeholder="Username" aria-label="Username">
    <span class="input-group-text">@</span>
    <input type="text" class="form-control" placeholder="Server" aria-label="Server">
  </div>
</div>
```

Checkboxes have a wrapping `div` with a special class `form-check` and radio buttons use it too. Validation messages require a style on helper text `div` and one on the field wrapper `div`.

## Tailwind (v2.1.0)

[Tailwind][tw] has little opinion as to how you should style your forms. The framework does not dictate any particular CSS although it does have a semi-official [Custom Forms][tcf] plugin. A text input: 

```
<label class="block">
  <span class="text-gray-700">Name</span>
  <input class="form-input mt-1 block w-full" placeholder="Jane Doe">
</label>
```

A checkbox:

```
<div class="flex mt-6">
  <label class="flex items-center">
    <input type="checkbox" class="form-checkbox">
    <span class="ml-2">I agree to the <span class="underline">privacy policy</span></span>
  </label>
</div>
```

Tailwind is more of a framework for building your own UI library and is therefore fairly agnostic about HTML structure. Although many of the same elements appear, a wrapping `div` for a field, a `label`, the form control itself alongside some help text in a `span`.

---

This is all relatively standard fare. We can see a lot of similarities in the approaches here, and indeed each project will require its own embellishments and definitions. A declarative form generator then needs to offer the flexibility to define how a form field will be rendered.

Is it possible to lift out the important, changeable elements so that they can be programmatically generated? Or should the approach be open enough for an end-user of the library to write their own component flavour with a set of these primitive dynamic elements? I think avoiding being prescriptive is the way to go. 

Another thought is that it would be interesting to chart the HTML element tree of different framework’s approaches to form rendering (as a graph/tree) to compare and contrast. I haven’t seen this kind of visual exploration and comparison of structure before.

—*Tuesday 20th April 2021.*

[cpf]: https://www.crossingtheruby.com/2021/04/19/form-formation-constituent-parts.html
[bufc]: https://bulma.io/documentation/form/general/
[bofc]: https://getbootstrap.com/docs/5.0/forms/overview/
[tw]: https://tailwindcss.com
[tcf]: https://tailwindcss-custom-forms.netlify.app
