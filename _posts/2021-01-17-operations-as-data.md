---
layout: post
title: Operations as Data
---

In [Ecto’s pure/impure separation][epi] we saw that Ecto separated out pure and impure operations, keeping both `Changeset` and `Query` modules largely pure compared with `Repo`’s interaction with the outside world of the data store. One of the key elements that makes this possible is encoding _operations as data_.

I first came across this in the Elm architecture. In the Model-View-Update cycle, events being triggered in the view layer did not call a function directly with a set of arguments but rather generated a message with all of the necessary details to later be able to process the event. The Elm runtime then sent this message to the update function where it could be executed.

Copied by Redux in React, this idea of defining the sum or range of all possible events, along with their shape or signature in terms of associated data, seems a critical abstraction in functional programming. I call it an abstraction because we are introducing a layer in between instruction and execution. (Not an abstraction layer in the vertical hierarchy of thought but a layer in between elements at the same horizontal level.) 

Normally you pass an argument to a function and then it is executed immediately. By treating operations as data, it is as if you are constructing the argument as a data structure that you will later pass to a function for execution. When the execution involves an impure operation then you can remain in the pure world until the last possible moment.

In addition, effectively queuing up an operation, and delaying its execution, allows you to both observe and manipulate the operation itself.

The observation is useful, for example, in retracing a program’s execution. Rather than having to introspect the machinery you can simply log the full details of the operation before it is executed. You have elevated the instruction to be a first class citizen of your application. 

Manipulation, as in the case of `Ecto.Changeset`, is similarly beneficial. By keeping the eventual operation—the create or update command to be executed against a data store—distinct from the details of the changes themselves it allows for the gradual construction and manipulation of the changeset. 

By lifting this step out and making it explicit there is a clear distinction between the original data and the data that will be changed. It also allows the casting, validating and modification of data to occur distinctly, before it hits the database layer. It, too, can be inspected and debugged easily. The steps involved in carrying out changes have been neatly mapped out rather than bundled together into something that later has to be pulled apart.

`Ecto.Query` has at its centre the `%Ecto.Query{}` struct. This is a little more involved since the data structure is an invisible element operating behind the scenes and working with it is relatively dynamic. It is transformed by a series of macros and functions rather than touching or modifying the data structure directly. The operation is therefore defined in the code as the combination of a set of composable function calls. This means that the process of constructing a query can be broken down into separate steps (distinct functions or macros with a common interface grouped together as appropriate; the query DSL) and facilitates a more granular level of reusability. 

And there are deeper benefits for Ecto’s particular use-case. By separating out the construction of the query from its transformation into an instruction set (e.g. an SQL query in the case of the PostgreSQL database) it creates a perfect plug-in point to be used against different data stores. It facilitates the adapter pattern by having a clean interface. An adapter can take the operation encoded as a data structure and transform it into the query language necessary for the relevant data store.

—*Sunday 17th January 2021.*

[epi]: https://www.crossingtheruby.com/2021/01/15/elixir-ecto-pure-impure-separation.html
