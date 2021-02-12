---
layout: post
title: "Elixir Module Pattern Refined"
---

In [Elixir Module Pattern][emp] I wrote about a common pattern in Elixir modules. A flow of initialisation and then transformation. That modules often operate on a single data type, that there is a way to initialize the data type and then that the rest of the functions in the module take the data (with that data type) as the first argument to effect a transformation and return the transformed data (of the same data type).

I mentioned Bruce Tate’s reference to this pattern as the ‘reducer’ pattern, that each transformation function acts as a step in a reducer function operating on the data returned from the previous step in the reducer. He has since written about this more explicitly in his post, [Learning Elixir: It’s All Reduce][btr]. 

I’m also working through the first four chapters of Saša Jurić’s excellent book, [Elixir in Action][eia] (Second Edition). As an aside, Mr. Jurić is an excellent verbal and written communicator. Listening to his talks is like connecting yourself to a fire-hose of unadulterated information. While I watch most technology related talks at 1.5–2× speed, with Mr. Jurić I’m more inclined to go in the other direction. His book is very much in a similar vein. It is refreshing and most rewarding to read an author who gives you the overwhelming feeling that he is respecting your time. No fluff, just stuff.

Anyway, when reading Chapter 4: Data abstractions, lo and behold, he also treats what I have called the Elixir Module Pattern in the opening section. He phrases it as follows, adding a key element to refine my own analysis:

> The basic principles of data abstraction in Elixir can be summarized as follows:
> - A module is in charge of abstracting some data.
> - The module’s functions usually expect an instance of the data abstraction as the first argument.
> - Modifier functions return a modified version of the abstraction.
> - Query functions return some other type of data.

> —Saša Jurić, _Elixir in Action: Second Edition_ (Shelter Island: Manning Publications), 103.

The first three aspects in the list are largely in line with what I’d experienced and written about. He uses the term “modifier” instead of my term “transformer” or Mr. Tate’s “reducer”. However, the last element in his list of properties with respect to Elixir modules is one I had not yet distinguished.

Modules provide functions to query the data structure. To retrieve data from the data structure without transforming it in any way. (If you squint a little, this resembles the distinction between _query_ and _command_ messages in Ruby which I learned from Sandi Metz and Katrina Owen.)

If we take the `Ecto.Changeset` module as an example then we can see this separation clearly. Below is a categorised list of the functions in that module according to Mr. Jurić’s definition:

```
INITIALISATION
  cast/4
  change/2
MODIFICATION
  cast_assoc/3
  cast_embed/3
  delete_change/2
  force_change/3
  merge/2
  put_assoc/4
  put_change/3
  put_embed/4
  update_change/3
  assoc_constraint/3
  check_constraint/3
  exclusion_constraint/3
  foreign_key_constraint/3
  no_assoc_constraint/3
  unique_constraint/3
  add_error/4
  unsafe_validate_unique/4
  validate_acceptance/3
  validate_change/3
  validate_change/4
  validate_confirmation/3
  validate_exclusion/4
  validate_format/4
  validate_inclusion/4
  validate_length/3
  validate_number/3
  validate_required/3
  validate_subset/4
  optimistic_lock/3
  prepare_changes/2
QUERY
  fetch_change/2
  fetch_change!/2
  get_change/3
  fetch_field/2
  fetch_field!/2
  get_field/3
  constraints/1
  validations/1    
TRANSFORMATION
  apply_action/2
  apply_action!/2
  apply_changes/1
  traverse_errors/2
```

(Note that I have ordered the functions within categories into related areas to group them for clarity.)

As you can see, the functions divide neatly into the three defined categories: initialisation, modification and query. However, there is a fourth category at the bottom that I have termed ‘transformation’. Now to disambiguate between my previous use of transformation (what Mr. Jurić calls modification) and the usage in this list:

- modification: the modification of the module’s primary data type into a changed form of the same data type.
- transformation: the transformation of the module’s primary data type into a _different_ data type.

Those functions in the transformation category are transforming a changeset, or part of the changeset into a format and type that is different to the changeset itself. (I see this as the ‘teardown’ vs. initialisation’s ‘setup’). These may not appear in all modules, but in an `Ecto.Changeset` they make sense because they signal the end of the changeset’s life cycle, after the changeset has successfully done its job and its purpose has been achieved.

I find this framework for categorizing the functions in a module helpful for getting an overall sense of the functionality that the module makes available.

And so, thanks to Mr Jurić, we have refined the Elixir Module Pattern to elevate _query_ functions as a first-class citizen, and we have discovered _transformation_ functions that transform the primary data structure into a different format.

—*Friday 12th February 2021.*

[emp]: https://www.crossingtheruby.com/2021/01/20/elixir-module-pattern.html
[btr]: https://redrapids.medium.com/learning-elixir-its-all-reduce-204d05f52ee7
[eia]: https://www.manning.com/books/elixir-in-action-second-edition
