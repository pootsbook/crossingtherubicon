---
layout: post
title: What is a Changeset?
---

Ecto, the Elixir package for interacting with data stores, introduces something called a changeset. A changeset is nothing other than a set of changes. In the context of Ecto, a set of changes that is intended to be committed to the data store. 

Yesterday I looked at the Ecto Changeset lifecycle from a very crude angle and now I’d like to dive a little deeper into precisely what an `Ecto.Changeset` is and does.

When we peel back the covers then it quickly becomes apparent that an Ecto Changeset (from now on just _changeset_) has a wide range of responsibilities and covers a lot of ground.

## Source Data

In essence, a changeset is a simply a wrapper around a set of key-value data, represented internally by a map:

```
%Ecto.Changeset{
  …,
  data: %{}
}
```

The rest of the changeset struct behaves like a meta-object which decorates this central, core data.

That meta-object contains a number of different elements all of which find their commonality in their service to this core data. We can look at each in turn.

## Data Definition

The data is map-like in structure and must also conform to a particular set of keys. For each key the value must conform to a corresponding type. This is what I’m calling the data definition.

This definition can be defined in several ways. The most basic is to provide a map of key-value pairs, the key corresponding to the key in the source data and the value containing the type information as an atom.

For an attribute of `name` with a type of string the data definition map would resemble:

```
%{name: :string}
```

What this does is instruct the changeset to only accept changes to a `name` attribute, and that this attribute must be typecast to a string.

This definition is stored on the changeset in the `types` field:

```
%Ecto.Changeset{
  …,
  data: %{},
  types: %{}
}
```

`types` contains just a straight copy of the map that was passed in. This definition is then used as a basis for typecasting the provided changes that are to be applied to the changeset.

## Record of Changes

So we have the original source data, the data definition and to that we can add a third element: a record of changes that want to be applied to the source data. This is perhaps more deserving of a central, core designation rather than the source data; a changeset is defined by the set of changes it contains.

The desired changes are also encoded as a map and are passed in on changeset creation or explicitly after creation. With source data of `%{name: "Phillip"}` we could provide the following change as a map: `%{name: "Philip"}`. In other words, our desire here is to transform the erroneous spelling of the name with the correct one, in pseudo-code: `%{name: "Phillip" => "Philip"}`

Like the source data and the data definition, this too is added to the changeset struct:

```
%Ecto.Changeset{
  …,
  data: %{},
  types: %{},
  changes: %{}
}
```

If we use our example to flesh out the changeset we start out with this:

```
%Ecto.Changeset{
  …,
  data:    %{ name: "Phillip" },
  types:   %{ name: :string },
  changes: %{ name: "Philip" }
}
```

## Parameters

There is one last ingredient at this initialization stage of the changeset. That is a record of the parameters that were passed in as intended changes. I’m not yet quite sure why these are persisted as it seems to overlap both the `data` and the `changes` properties. Perhaps it is simply a matter of bookkeeping.

These parameters are stored in the changeset in the params field:

```
%Ecto.Changeset{
  …,
  data: %{},
  types: %{},
  changes: %{},
  params: %{}
}
```

In our example the params map is identical to the changes map:

```
%Ecto.Changeset{
  …,
  data:    %{ name: "Phillip" },
  types:   %{ name: :string },
  changes: %{ name: "Philip" },
  params:  %{ name: "Philip" }
}
```

The above changeset is created using the `Ecto.Changeset.cast/4` function.

## `Ecto.Changeset.cast/4`

`Ecto.Changeset.cast/4` is responsible for pulling together the disparate parts required to build a changeset and in doing so both filters and typecasts the input. 

Filtering is accomplished by providing a list of atoms with keys that we wish to include in the changes map. This is a defensive move to ensure that we only change data that we explicitly want to change.

Typecasting is done on the basis of the type information provided.

Putting all of the pieces together:

```
Ecto.Changeset.cast(
  {
    %{name: "Phillip"},
    %{name: :string}
  }, 
  %{name: "Philip"}, 
  [:name])
```

To recap this has done a number of things:

1. Stored the source data `%{name: Philip}`  
(first element of the tuple passed as the first argument)
2. Stored the type data `%{name: :strong}`  
(second element of the tuple passed as the first argument)
3. Stored the params data `%{name: "Philip"}`  
(second argument)
4. Filtered the params data to only include the data from the specified keys in the changes map  
(the list of keys in the third argument)
5. Calculated the desired changes based on (a) the filtered params and (b) the values in the params that represent a change from the source data  
(We could have passed in a set of params that was identical to the source data and thus required no changes)

And we have our changeset. Next we move onto what we can do with it.

—*Sunday 24th January 2021*
