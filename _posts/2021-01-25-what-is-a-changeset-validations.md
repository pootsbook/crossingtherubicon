---
layout: post
title: "What is a Changeset?: Validations"
---

Validations sit at the heart of a changeset’s purpose. After we have specified what precisely we want to change, the next step is to ascertain whether or not the proposed changes are valid with respect to the rules in our business logic. We can define these rules as part of the changeset using validations.

`Ecto.Changeset` provides a number of built-in validations while also providing us with all the tools necessary to create our own. The built-in validations cover common use-cases while custom validations give us the opportunity to cover less common scenarios specific to our applications.

In each case, the validation function receives a changeset as its first argument and the field requiring validation as its second. The provision of further arguments depend on the type of validation that is to be applied. 

For example, if we want to validate the format of a string that is being changed we can use the `Ecto.Changeset.validate_format/4` function. The third argument in this function is a regular expression, while the fourth (optional) argument is a keyword list of options.

Let’s say I’m a little too enthusiastic about the correct spelling of my name and add an exclamation mark. What happens if I add a validation that forbids special characters in the name field? I can use `validate_format/4` together with a regex allowing only word characters (`\w`):

```
import Ecto.Changeset

source_data = %{name: "Phillip"}
types       = %{name: :string}
params      = %{name: "Philip!"}

changeset = cast({source_data, data}, params, [:name])
validated_changeset = validate_format(changeset, :name, ~r/\A\w+\z/)
```

A new changeset is returned with some modifications. The validations are stored in the changeset on the `validations` field and the question as to whether or not the changeset is valid is contained in the `valid?` field:

```
%Ecto.Changeset{
  …,
  data: %{},
  types: %{},
  changes: %{},
  params: %{},
  validations: [],
  valid?: true|false
}
```

So contrary to my hunch that validations were built up as data before being executed is proved incorrect. When we run the changeset through the validate function then it not only adds the validation to the list of validations to be applied, but it also applies the validation, updating the `valid?` result. 

The changeset is an immutable data structure so it makes little sense to have a potentially inconsistent state by separating the storage of the validation (as data) from the execution of that same validation.

In our case the validation fails because there is a special character in the `name` field. As a result we are introduced to another aspect of the changeset closely related to validations: errors.

If a validation fails then it results in an error. The `valid?` property of the changeset will be set to `false`. In order to locate precisely what data failed validation, and why, a new field is populated on the changeset, the errors keyword list:

```
%Ecto.Changeset{
  …,
  data: %{},
  types: %{},
  changes: %{},
  params: %{},
  validations: [],
  valid?: true|false,
  errors: []
}
```

This list contains a mapping from the key of the field which failed validation to an error record. The error record is a tuple containing an error message as a string and a keyword list pointing to the source of the error. In this particular case the format validation.

So if we flesh out the changeset after having put it through the format validation it looks like this:

```
%Ecto.Changeset{
  …,
  data:        %{ name: "Phillip" },
  types:       %{ name: :string },
  changes:     %{ name: "Philip!" },
  params:      %{ name: "Philip!" },
  validations: [name: {:format, ~r/\A\w+\z/}],
  valid?:      false,
  errors:      [name: {"has invalid format", [validation: :format]}]
}
```

Everything we have done with the changeset from the very beginning of its lifecycle has been stored as data in the changeset struct itself. So far, any operation carried out on the changeset is simply a transformation of the changeset; the relevant data is added to the corresponding fields.

—*Monday 25th January 2021.*
