---
layout: post
title: "Hypertext DSL: Template Variables and Helpers (2)"
---

In the last post I [successfully combined][hvh] experiments with [variables][hve] and [helpers][hhe] together with the Hypertext DSL.

My curiosity got the better of me, as it often does, and I wanted to see how Markaby, a partial inspiration for this templating approach, accomplished the same goal.

In the `Markaby::Builder` class, which is analogous to the `Hypertext::DSL` class (or module, in my variation) the `initialize` method takes both variables and a helper object as parameters in addition to the template block to be evaluated, assigning them both to instance variables.

```
module Markaby
  …
  class Builder
    …
    def initialize(assigns = {}, helper = nil, &block)
      …
      @assigns = assigns.dup
      @_helper = helper
      …
    end
  end
end
```

For the assigns, what I called variables or params, it uses the same trick to set instance variables via the `instance_variable_set` method (also as part of the `initialize` method):

```
@assigns.each do |k, v|
  instance_variable_set("@#{k}", v)
end
```

In addition, it also maps the instance variables of the helper object to instance variables on the builder object by defining and assigning them using the same technique:

```
if helper
  helper.instance_variables.each do |iv|
    instance_variable_set(iv, helper.instance_variable_get(iv))
  end
end
```

So how are helper methods then exposed to the builder context? Well, no popular Ruby library worth its salt would be complete without a strong dose of `method_missing`, would it?

The faint of heart should look away now. And apparently this `method_missing` definition was previously the “lynchpin” in Markaby, so it used to be even more complicated. I’ll lift out only the branches that interest us here:

```
def method_missing(sym, *args, &block)
  if @_helper.respond_to?(sym, true)
    @_helper.send(sym, *args, &block)
  elsif @assigns.has_key?(sym)
    @assigns[sym]
  …
  elsif instance_variables_for(self).include?(ivar = "@#{sym}".to_sym)
    instance_variable_get(ivar)
  elsif @_helper && instance_variables_for(@_helper).include?(ivar)
    @_helper.instance_variable_get(ivar)
  …
  end
end
```

So we can see that if a method is called in the builder that is not recognised, i.e. a method that is not defined by the HTML-focused `Markaby::BuilderTags` module, then it will be picked up by this `method_missing` definition and will travel down its logic branches, including—but not limited to—the four branches reproduced above.

The first branch `if @_helper.respond_to?(sym, true)` checks if the message that has been sent to the `Markaby::Builder` object matches a method on the helper that was passed in on initialization and stored in the `@_helper` instance variable. If that is the case, then the message is sent to the helper, or in other words, the helper method is called.

The second branch checks if the method matches any of the assigns that were passed into the `Markaby::Builder` object on initialization, and if so, it looks up the corresponding value on the `@assigns` instance variable.

The third branch had me scratching my head. I didn’t find any Ruby documentation for `instance_variables_for`. Now in these cases you should always trust `ri` above Google, and sure enough we learn the following:

```
$ ri instance_variables_for
= .instance_variables_for

(from gem markaby-0.9.0)
=== Implementation from Builder
------------------------------------------------------------------------
  instance_variables_for(obj)

------------------------------------------------------------------------
```

This is a method that Markaby defines itself. Despite my suspicions, it is actually a harmless wrapper for `Object#instance_variables` and deals with some Ruby version compatibility issues; versions below 1.9.1 need methods converted to a symbol and versions above do not.

So basically this third branch is converting a method call to an instance variable of the same name, if it exists, and returning the value of the instance variable.

And the fourth and last branch is a variation on the third, but instead of checking instance variables on the builder object (`self`) it checks the helper object (`@_helper`).

All in all, both pretty innocuous and useful in wrapping both variables and helpers into the main API and exposing them to the appropriate context. In a future post I’ll experiment with a similar API using Hypertext DSL. 

—*Monday 29th March 2021.*

[hvh]: https://www.crossingtheruby.com/2021/03/28/hypertext-dsl-template-variables-helpers.html
[hve]: https://www.crossingtheruby.com/2021/03/24/hypertext-dsl-template-context-variables.html
[hhe]: https://www.crossingtheruby.com/2021/03/27/hypertext-dsl-template-context-helpers-2.html
