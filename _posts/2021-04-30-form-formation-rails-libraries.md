---
layout: post
title: "Form Formation: Rails Libraries"
---

There are a number of libraries in the Ruby ecosystem which aid in the creation of forms. Some of these are built to be used within Rails, others specifically for other frameworks, and others that are framework-agnostic.

In this post I’ll look at popular libraries intended to be used with Rails. The following is not an exhaustive list but is a starting point on my journey to analyse and categorise these libraries.

## Rails

[ActionView::Helpers::FormHelper][avhfh] is a module containing the default set of helper methods for working with forms in Rails.

[Formtastic][ft] is a Rails FormBuilder DSL. It promises easier form definitions that offer several advantages over the defaults in Rails.

[Simple Form][sf] inherits much of the Formtastic DSL but is a separate library with a focus on flexibility alongside separating form definition and layout concerns.

[YAAF][yaaf] is slightly different in that it builds on ActiveRecord and ActiveModel to provide an easier way to define what I call the form object. It has no involvement or opinions about the HTML side.

There are several other libraries that could have made it into this list, but I believe the first three represent the majority of usage for HTML form generation in the wild. The last one represents an interesting development of the form object built on top of Rails primitives. 

Other libraries seem like slight variations on the same theme and less worthy of investigation. If you know of other libraries that would be worth looking at in the Rails ecosystem then I’m all ears.

In a future post I will look at the libraries that other Ruby frameworks use for form generation.

—*Friday 30th April 2021.*

[avhfh]: https://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html
[ft]: https://github.com/formtastic/formtastic
[sf]: https://github.com/heartcombo/simple_form
[yaaf]: https://github.com/rootstrap/yaaf
