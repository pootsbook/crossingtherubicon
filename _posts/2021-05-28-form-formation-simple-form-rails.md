---
layout: post
title: "Form Formation: Simple Form (Rails)"
---

Simple Form is a forms library for Rails that takes much inspiration from [Formtastic][ft]. The DSL is inherited from, and therefore almost identical to, Formtastic and it works in much the same way.

The key distinction is (or at least, was) that Simple Form is more flexible with respect to defining your own markup.

> The basic goal of Simple Form is to not touch your way of defining the layout, letting you find the better design 

It is designed to be customised. The small relative popularity of Simple Form over Formtastic is perhaps an indication that this ability to customise is prized by developers and may be a useful data point.

The interesting thing in our imperative/declarative discussion is that Simple Form is reading the data type of a particular attribute from the ActiveRecord object and the associated database column definition.

> Simple Form acts as a DSL and just maps your input type (retrieved from the column definition in the database) to a specific helper method.

This means that it is using the implicit information that is available to it without having to define it explicitly in the creation of the form. It is of course possible to override this default if desired.

The aim of Simple Form is also to be comprehensive. Where a Rails input helper contains only the `input` HTML form control, Simple Form generates both the label and the input (similar to Formtastic) as well as hints, errors, and placeholders, all within a wrapper element.

```
<%= simple_form_for @user do |f| %>
  <%= f.input :username %>
<% end %>
```

The above code will generate a complete set of HTML tags, including an `input` of type `text` based on the fact that `User#username` is a `string` type in the database. A table with the default mapping is available in the [README][map].

I like this declarative element, although it is very Rails to have it implicit rather than explicit. 

I didn’t see a way of generating a complete form for a given model like in Formtastic.

Almost everything you do with Simple Form is customisable so you are not being forced into a box or a specific way of doing things.

It builds on top of the helpers and options in standard Rails which is great if you know Rails well but at a distance seems like you end up with a lot of options hashes. Maybe this is unavoidable.

It is certainly worth a deeper look given its status as the most popular Rails’ form library.

—*Friday 28th May 2021.*

[ft]: https://www.crossingtheruby.com/2021/05/27/form-formation-formtastic-rails.html
[map]: https://github.com/heartcombo/simple_form#available-input-types-and-defaults-for-each-column-type
