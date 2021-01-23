---
layout: post
title: Ecto.Changeset Lifecycle
---

As I mentioned in the [Elixir Module Pattern][emp] it can sometimes be helpful to look at functional modules through the lens of classes in object-oriented programming. In OOP one of the standard actions is the instantiation, creation or initialization of an object. Then the object takes its place in the object graph, interacts with other objects (hopefully doing something useful), and having done its job, disappears when the garbage collector makes the rounds.

In `Ecto.Changeset` we can see this pattern in its own lifecycle. A changeset is created, it is used for its purpose, and when it is done it offers up the data it has been managing, passes it on to someone else and then disappears silently into the night. 

Rather than an object, the focus of its attention is a data structure, the `%Ecto.Changeset{}` struct. We can distinguish the operations on the data structure into three broad categories which can each be examined in turn. The changeset is:

1. Created
2. Manipulated
3. Delivered

The meat and potatoes of the module are found in the manipulation step. Creation and delivery are (important) book ends. And if we want to be strictly correct, there is some overlap between each of the three stages, but I think these distinctions are helpful.

## Creation

There are two functions which create or initialize a changeset:

- `Ecto.Changeset.cast/4`
- `Ecto.Changeset.change/2`

They represent two slightly different use-cases. `cast/4` is for taking input external to your application, input that will likely be rough, raw, and unsafe. `change/2` takes input internal to your application, or at least input where you have some guarantees and assurances that it will behave itself.

## Manipulation

The manipulation of a changeset is too involved to look at in any great detail, but the key elements here are:

- the addition of validations, 
  - `Ecto.Changeset.validate_*`
  - `Ecto.Changeset.unsafe_validate_*`
- the addition of constraints,
  - `Ecto.Changeset.*_constraint`
- the access and modification of a changeset and its data.
  - `Ecto.Changeset.put_*`
  - `Ecto.Changeset.get_*` 
  - `Ecto.Changeset.fetch_*`

Similar to what we have discussed in previous posts, we are building up the changeset struct with operations as data (like validation and constraints) before executing them.

## Delivery

Delivery is the act of execution, validating the changeset and if valid handing it on to the next step on the chain. This is usually a data store. Where the lines get a little blurred (and I would like to explore this some more) is that the data store can also modify the changeset if the data in the changeset is unable to be committed. 

In the case where the data from the changeset can be committed then the changeset has effectively done its job and can safely be discarded. This completes the lifecycle.

—*Saturday 23rd January 2021.*

[emp]: https://www.crossingtheruby.com/2021/01/20/elixir-module-pattern.html
