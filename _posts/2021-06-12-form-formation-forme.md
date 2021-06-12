---
layout: post
title: "Form Formation: Forme"
---

The last library on my shortlist for investigating the creation of HTML forms in Ruby is [Forme][fe]. Forme was written by Jeremy Evans, creator of the Roda web framework, and is one of the impressive number of libraries designed to be used within Roda. 

His design philosophy, similar to what we have seen with Hanami, is geared towards small, self-contained libraries that are then pieced together to form a greater whole. The implication is that a library like Forme is not tied to any particular framework.

Forme is designed with flexibility and simplicity in mind. It supports building forms with or without a related object. One interesting feature is that it is designed to be able to compile down to different types of output. HTML is only one such output.

Nevertheless, like other libraries before it, it signals the close interconnectedness of forms and HTML by building its abstraction around a `Forme::Tag` which is essentially an abstract representation of an HTML tag.

It is noteworthy that all of the Ruby form libraries that live outside of the Rails ecosystem have implemented some kind of HTML generation scheme. And funny that my own journey towards a new kind of form library started with [Hypertext][ht].

The signature of the `Forme::Tag` initializer is identical to that of a simple HTML abstract syntax tree with the one difference being that a `form` variable is passed as the first argument:

```
module Forme
  class Tag
    …
    def initialize(form, type, attr={}, children=nil)
      @form, @type, @attr = form, type, (attr||{})
      @children = parse_children(children)
    end
    …
  end
end
```

This is then integrated via a form builder pattern:

```
f.tag(:select, {}, [
  f.tag(:option, { value: 1 }, [
    'foo'
  ]
end
```

There is also another, more abstract class, `Forme::Input` which allows constructing a form control and the associated HTML at a higher-level.

```
f.input(:select, :options=>[['foo', 1]])
```

You get full control over the process of transforming this `Forme::Input` object into HTML. There is a structured and programmatic approach for customising the HTML. This looks like a neat solution to the tension between the automation of forms and the unique aspects that an individual form might need in relation to the standard.

The list of these _transformers_ is as follows:

- Formatter (how to convert to a input to a tag)
- ErrorHandler (how to indicate errors if present)
- Helper (for adding/configuring help text)
- Labeler (for adding/configuring a label)
- Wrapper (option to wrap form elements)
- Serializer (converts to string)

It is a very helpful breakdown of all of the concerns involved in producing the HTML for a form control. And it is codified in Forme.

This promises both automation and flexibility. It will be interesting to see whether this holds true.

—*Saturday 12th June 2021.*

[fe]: http://forme.jeremyevans.net
[ht]: https://www.crossingtheruby.com/2021/03/14/hypertext-write-html-in-ruby.html
