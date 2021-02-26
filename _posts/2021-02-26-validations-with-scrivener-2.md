---
layout: post
title: Validations with Scrivener (2)
---

In a [previous post][vs] I looked at how you might use Scrivener, a minimal validation library for Ruby by Michel Martens, to filter and validate a set of data. 

We came across two validation methods or assertions in `assert_present` and `assert_numeric`. There are a number of other built-in methods that are all relatively self-explanatory. Here is the full list:

```
assert_present
assert_equal
assert_format   # against a Regexp
assert_numeric
assert_url
assert_email 
assert_member   # of an Enum
assert_length 
assert_decimal
```

Each of the methods takes as its first argument a symbol representing the field on which the validation should be applied and, if necessary, a second argument with details of what data should be used as a basis for the validation (e.g. a regular expression or a given length).

All of the above validations are based on a primitive `assert` method which then also forms the basis for any custom validations that you might wish to write.

To fully understand the `assert` method we can take a detour into the format of the errors hash. This is a plain Ruby Hash with model attributes as keys mapping to an array of error codes, allowing multiple errors to be logged for any given field:

```
{ :age =>[:not_numeric] }
```

Each Scrivener sub-class instantiates this hash in the `@errors` instance variable and makes it accessible via a method of the same name. Validations are able to modify this hash as necessary.

This is the definition of `assert`:

```
def assert(value, error)
  value or errors[error.first].push(error.last) && false
end
```

`value` is an expression that evaluates to a boolean; `error` is a tuple, masquerading as an two-element array, with the attribute key and the error code, e.g. `[:name, :incorrect]`.

If you look at the implementation of `assert_present` then you can see how it uses `assert` under the hood:

```
def assert_present(att, error = [att, :not_present])
  assert(!send(att).to_s.empty?, error)
end
```

It defines a default error of `:not_present` on the attribute that is passed in, and it asserts that when sending the attribute to `self` that it isn’t empty.

And these can build on top of each other, for example in `assert_format` it builds on `assert_present`:

```
def assert_format(att, format, error = [att, :format])
  if assert_present(att, error)
    assert(send(att).to_s.match(format), error)
  end
end
```

If the value isn’t present then it won’t bother matching the regular expression against the given attributes value.

And `assert_decimal` builds on `assert_format`:

```
DECIMAL = /\A\-?(\d+)?(\.\d+)?\z/

def assert_decimal(att, error = [att, :not_decimal])
  assert_format att, DECIMAL, error
end
```

And we therefore have this wonderful Russian Doll style composition of validations built around one primitive method, `assert`.

It’s easy to see how you might begin to put together your own validations based on these primitives, and that’s what I’ll do in a future post.

The design is incredibly elegant. Simple to understand, simple to extend and compose. It doesn’t get in your way but encourages you to build up a set of cohesive validations, either on the fly in a given Scrivener sub-class, or in a separate module to be included.

—*Friday 26th February 2021.*

[vs]: https://www.crossingtheruby.com/2021/02/25/validations-with-scrivener.html
