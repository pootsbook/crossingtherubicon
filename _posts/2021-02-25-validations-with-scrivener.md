---
layout: post
title: Validations with Scrivener
---

[Scrivener][sv] is another minimalist Ruby library that deals with validations. It is designed for a clear separation of concerns and is not attached in any way to a particular model or form implementation.

We have seen that [validations in Ecto][ev] are bundled together with multiple other concerns in an `Ecto.Changeset`. In Rails validations are baked into ActiveRecord models. Scrivener takes a different approach.

What all three approaches agree on is that the primary data structure concerned in these validations is a Hash (Ruby) or a Map (Elixir). This maps well to the output of an HTTP form which consists of a set of key-value pairs.

Another point of agreement between Ecto and Scrivener is that validation and filtering work hand in hand. Validation is a check to ascertain whether or not a given piece of data conforms to expectations; filtering is the rejection of unnecessary or unwanted data before the act of validation. 

Rails prefers to leave filtering at the controller level, distinct from validations. It uses [`StrongParameters`][sp] at the controller layer to filter out unwanted input. This assumes data is coming from the controller layer. The following code will reject, or filter out, the `profession` key and its value:

```
params = { 
  person: { 
    name: "David", 
    age: 41, 
    profession: "programmer" 
  } 
}
params.require(:person).permit(:name, :age)
```

## Filters

In Scrivener you define an object as a subclass of the `Scrivener` class:

```
class CreateUser < Scrivener
end
```

In order to define a set of fields that will be used to filter incoming parameters you simply use the well-known `attr_accessor` Ruby class method:

```
class CreateUser < Scrivener
  attr_accessor :name
  attr_accessor :age
end
```

This defines a filter that will only accept a `name` and an `age` parameter. Let’s give it a go:

```
params = {
  name: "David", 
  age: 41, 
  profession: "programmer"
}
filter = CreateUser.new(params)
filter.attributes
#=> { :name => "David", :age => 41 }
```

As you can see, the extraneous `profession` key has been _filtered_ out.

## Validations

Scrivener also allows for validations. It comes with a set of built-in validations and the capability to define your own. This is standard, similar to Ecto and Rails. To define the set of validations you wish to apply you simply write a `validate` method in your Scrivener sub-class which includes a list of validations that you wish to apply in the form of assertions: 

```
class CreateUser < Scrivener
  attr_accessor :name
  attr_accessor :age
  
  def validate
    assert_present :name
    assert_numeric :age
  end
end
```

Sending the `valid?` message to the object will execute the `validate` method and either return `true` in the case that assertions have been met, or `false` in the case of invalid fields. Should there be invalid fields then a map of errors will be available by sending the `errors` message to the object:

```
params = { name: "Philip", age: "NaN" }
filter = CreateUser.new(params)
filter.valid?
#=> false
filter.errors
#=> { :age =>[:not_numeric] }
```

The Scrivener object can be treated as a normal Ruby object and the value of the `age` field updated in the normal fashion:

```
filter.age = 35
filter.valid?
#=> true
```

It is refreshingly simple and straightforward and builds on the underlying native Ruby objects to achieve a familiar interface. There’s very little magic involved and it is very flexible in usage. 

In future we can dive a little deeper into how it can be extended to define custom validations and perhaps even pull open the bonnet to see how things work inside; at 251 significant lines of code it won’t be a considerable challenge.

–*Thursday 25th February 2021.*


[sv]: https://github.com/soveran/scrivener
[ev]: https://www.crossingtheruby.com/2021/01/25/what-is-a-changeset-validations.html
[sp]: https://api.rubyonrails.org/classes/ActionController/StrongParameters.html
