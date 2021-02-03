---
layout: post
title: Elm Result
---

Similar to [Elm’s Maybe type][emt], Elm’s `Result` type is a means of taking something that would otherwise be implicit and [making it explicit][eoi]. `Result` models an operation that can succeed or fail, returning a value on success, and a value on failure. 

The difference from a conventional return value is that in the case of `Result` the value is additionally tagged as success or failure. It bundles the success or failure of the operation along with the return value from that operation. Similar to `Maybe` which lifts null (or `Nothing`) into your program explicitly, `Result` lifts the concept of success and failure into your program explicitly. 

This obviates the need for a null check (failure _implicit_ in the return value) before splitting success and failure branches. The danger here is that you forget to check for the failure case and move on, leaving a ticking time bomb behind. By using the `Result` type both paths have to be dealt with or the program won’t compile. It makes the failure case _explicit_. You can’t ignore it.

This is what it looks like in Elm:

```
type Result error value
  = Ok value
  | Err error
```

For a string error message and an integer success value the type definition would look something like this:

```
Result String Int
```

And using the simple example of converting a string to an integer you could return this particular `Result` type in both success and failure cases:

```
case String.toInt input of
  Nothing ->
    Err "NaN"
  
  Just number ->
    Ok number
```

We know that if it errors we get a string, which we could later display, or if it succeeds we get an integer, which we can then further use in our program. For an `input` value of `"1"` we’d get a `Result` of `Ok 1`; for an `input` value of `"hello"` we’d get a `Result` of `Err "NaN"`.

The value is packaged up and can be passed around without our application blowing up unexpectedly. When the time comes to unwrap the value we can do so safely by being forced to explicitly deal with both cases. It covers all bases. There is nothing implicit lurking inside of our code to surprise us.

—*Wednesday 3rd February 2021.*

[emt]: https://www.crossingtheruby.com/2021/02/01/maybe-elm.html
[eoi]: https://www.crossingtheruby.com/2021/02/02/explicit-over-implicit.html
