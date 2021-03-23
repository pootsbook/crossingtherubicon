---
layout: post
title: "Hypertext DSL: Nested Templates"
---

After yesterday’s post exploring using the Hypertext DSL as a [templating engine][hte], a new version of Hypertext was released [(0.0.4)][hnr]. It incorporates a variant of the monkey patch I had presented using an `append` method to allow nested templates:

```
class Hypertext
  …  
  def append(value)
     @dom.push(*value)
  end
  …
end
```

```
class Hypertext::DSL
  …
  def append(content)
    @ht.append(content)
  end
  …
end
```

It’s nice to see this officially land. An email with an idea from Mr. Martens, and then a blog post from me exploring the idea, and now an extension of the library to assimilate what was learned. 

The one difference is that my implementation of `Hypertext#append` added the fragment as an element to the `@dom` array (`@dom << fragment`) whereas Mr Marten’s official version prefers `@dom.push(*fragment)`. This is a subtle difference which ensures the indentation remains consistent.

I have been thinking of different approaches to encapsulating templates and exposing variables to templates.

One is to use Markaby’s approach of a template method which loads a file and evaluates it in a certain context.

Another is to wrap templates in classes and expose instance variables which is perhaps a nod in the direction of Rails.

Yet another, similar to yesterday, is wrapping templates in methods and using standard parameters, or one Hash parameter, to expose data to a template.

I’ll explore those in future posts.

—*Tuesday 23rd March 2021.*

[hte]: https://www.crossingtheruby.com/2021/03/22/hypertext-dsl-templating-explorations.html
[hnr]: https://github.com/soveran/hypertext/commit/6d452d74d446353cac9a4db8e0a04f2c57c77c6f
