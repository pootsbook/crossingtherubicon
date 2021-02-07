---
layout: post
title: "What is a Changeset? Constraints"
---

In previous posts [What is a Changeset?][wic] and [Validations][wicv] we have been building up a changeset from nothing to take a look at all its constituent parts. This post deals with constraints.

A constraint is tightly linked to a data store. You can look at it as a kind of validation, but one that happens at the level of the data store. 

A normal validation happens at the level of your code. Considering the particular case of the PostgreSQL database, which is the most popular data store used with Ecto, a constraint happens at the level of a database constraint.

A database constraint is a tool that _constrains_ the data entering the database. It is normally used to ensure data consistency. It plays very much the same role as a validation but is one that is enforced at the most fundamental level, the row in a data store.

Why would you use a constraint over a validation? The archetypal example is a uniqueness constraint versus a uniqueness validation. A validation can only respond at the moment the validation is run and can thus provide no guarantees that an identical value hasn’t been submitted to the database in the time window between the execution of the validation and the committing of the value to the database. Relying only on the validation could therefore produce inconsistent data.

By adding a uniqueness constraint we use the guarantees that the database can give us. That is, if we add a unique index on a database column then the database cannot add two identical values in that same  column. Instead, it raises an error should we attempt to do that.

Ecto even goes so far as to highlight validations that might not give us the appropriate guarantee, e.g., `unsafe_validate_unique/4`. This is a convenience method to avoid having to commit to the database to find out if a value is unique or not, but it is clearly labelled as being ‘unsafe’.

The equivalent constraint in this case, assuming the presence of a uniqueness index, is `unique_constraint/3`. Unlike validations, which are executed immediately on the changeset, and before a changeset is sent to the database, constraints are a way of encoding database errors as data and will only be used during an attempt to commit the data to the database. For example, if a uniqueness constraint is added to the changeset, then this lets the changeset know that there is a potential for the database to produce an error when committing changes due to a duplicate value in the given column. If the changeset contains details of this constraint, then the changeset is able to handle the database error and transform it into a changeset error. 

A changeset constraint uses the same error reporting mechanism as a validation, adding an appropriate error record to the errors field on the changeset struct. Adding a constraint to the changeset is also done similarly to validations with the application of a `*_constraint` function adding details to the `constraints` key of the changeset struct:

```
%Ecto.Changeset{
  …,
  data: %{},
  types: %{},
  changes: %{},
  params: %{},
  validations: [],
  valid?: true|false,
  errors: [],
  constraints: []
}
```

If we were to have a User schema with a uniqueness validation on an email field, complete with a database uniqueness constraint then our constraint would look like this:

```
import Ecto.Changeset

changeset = cast(%User{}, {email: "duplicate@example.com"}, [:email])
changeset = unique_constraint(changeset, :email)
changeset.constraints

[
  %{
    constraint: "users_email_index",
    error_message: "has already been taken",
    error_type: :unique,
    field: :email,
    match: :exact,
    type: :unique
  }
]
```

If we were to try and commit this new record with a duplicate email address to the database then Ecto would convert the constraint error from the database on this field to the following changeset error:

```
{:error, changeset} = Repo.insert(changeset)
changeset.errors

[
  email: {"has already been taken",
   [constraint: :unique, constraint_name: "users_email_index"]}
]
```

If we then flesh out our changeset with the addition of a constraint and the corresponding error, then it looks like this:

```
%Ecto.Changeset{
  …,
  data:        %User{ email: nil },
  types:       %{ email: :string },
  changes:     %{ email: "duplicate@clubcollect.com" },
  params:      %{"email" => "duplicate@example.com"},
  validations: []
  valid?:      false,
  errors:      [
    email: {"has already been taken",
     [constraint: :unique, constraint_name: "users_email_index"]}
  ]
  constraints: [
    %{
      constraint: "users_email_index",
      error_message: "has already been taken",
      error_type: :unique,
      field: :email,
      match: :exact,
      type: :unique
    }
  ]
}
```

The constraint itself is a map containing all of the relevant detail necessary to capture the database error and transform it into a changeset error. The constraint error has the same format as a validation error. This gives the benefit of treating both validations and constraints similarly when presenting error messages to the end user.

—*Sunday 7th February 2021.*

[wic]: https://www.crossingtheruby.com/2021/01/24/what-is-a-changeset.html
[wicv]: https://www.crossingtheruby.com/2021/01/25/what-is-a-changeset-validations.html
