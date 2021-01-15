---
layout: post
title: Pure-Impure Separation in Elixir’s Ecto
---

In [Functional Essence][fe] and [Stateless Boundaries][sb] I touched upon the tendency in functional programming to divide the pure from the impure as a means of improving application design. I want to look at this from the perspective of Elixir’s database library, Ecto.

Ecto is a concrete example of pure-impure separation within a library written in a functional programming language. There are four main components in Ecto, housed in the following modules:

- `Repo` (repository) for communication with a data store,
- `Schema` to map a data source to an Elixir struct,
- `Changeset` to filter, cast, validate and track changes to a data structure,
- `Query` to build queries for information retrieval.

Of the four components, the first, `Repo`, is the component that deals with the outside world of the data store. Interactions with the data store have the potential to be impure, they can modify the state of the world. The other three components are all separated from the outside world and only interact with it by going via `Repo`. There may be one or two exceptions (e.g. `Changeset.unsafe_validate_unique/4` communicates with the database) but in general this distinction holds true.

A changeset, rather than executing a set of changes immediately, allows us to build up a declaration of all of the things we would like to change. When we want to execute the change then we pass the changeset to the repository and the repository is responsible for carrying it out. This is in stark contrast to, say, Ruby’s ActiveRecord. There we modify an object on the fly and the accounting for changes is handled as part of the object’s state. Changes are persisted to the datastore by sending the object the appropriate message. The lines are blurred.

Similarly with `Query` we are building up a declaration of the query we would like to make to the data store. When we want to execute the query against the data store then we pass it to the repository, and the repository is responsible for carrying it out and returning the results to us. In ActiveRecord, it’s not always clear at what point a query is executed and this can sometimes lead to surprising and unintended results. Often these relate to a degredation in performance due to queries being executed when you don’t expect them.<sup>1</sup> The lines are again blurred. 

When responsibilities are clearly delineated, as here in the case of Ecto, then it lends greater confidence. By building up a set of changes as a distinct step from the execution of those changes against the data store we never have to second guess when and where our changes are happening. By constructing a query as a distinct step from the execution of that query against the data store we never have to second guess when and where our database queries are being executed. There is less room for misunderstanding, there is a cleaner cognitive workspace, the code is easier to read and to comprehend, and the debugging process is faster by narrowing down the range of potential sources of a bug.

—*Friday 15th January 2021.*

<div class="footnotes">
  <ol>
    <li>
      See <a href="https://www.speedshop.co/2019/01/10/three-activerecord-mistakes.html">3 ActiveRecord Mistakes That Slow Down Rails Apps: Count, Where and Present</a> by Rails performance expert Nate Berkopec to get a flavour of what I mean.
    </li>
  </ol>
</div>

[fe]: https://www.crossingtheruby.com/2021/01/04/functional-essence.html
[sb]: https://www.crossingtheruby.com/2021/01/05/stateless-boundaries.html
