---
layout: post
title: "Validations with Scrivener (3): Custom Validations"
---

In previous posts I covered [model validations with Scrivener][mvs] and looked at how [validations are neatly composed][svc] from a single primitive. In this post I want to build a custom validation and discuss different ways they could be integrated with your Scrivener validation objects.

## BIC Validation

The first validation will be validating the format of a BIC. A BIC is a Bank Identifier Code, also known as a SWIFT code, which is used to identify banks and financial institutions globally. A little like an international bank ID.

It is an 8–11 character code that identifies the country, city, bank and branch code of a given bank. It has the following format:

```
AAAA  # bank code     (A-Z)     [4]
BB    # country code  (A-Z)     [2]
CC    # location code (0-9 A-Z) [2]
123   # branch code   (0-9 A-Z) [3]
```

My bank has the BIC `RABONL2UXXX`. 

To validate a BIC we must validate the _format_ of the string that is passed in as an attribute. So we can lean on Scrivener’s built in `assert_format` validator. To follow the pattern established with `validate_decimal` we’ll define the regular expression in a constant:

```
BIC = /\A[A-Z]{6,6}[A-Z2-9][A-NP-Z0-9]([A-Z0-9]{3,3}){0,1}\z/

def assert_bic(att, error = [att, :not_bic])
  assert_format att, BIC, error
end
```

Note that we provide a default error symbol of `:not_bic` in the parameters, which will allow a caller to customise if desired.

Now we are free to use this validator in our `validate` function within our Scrivener object:

```
class CreateBank < Scrivener
  attr_accessor :name
  attr_accessor :bic
  
  def validate
    assert_present :name
    assert_bic :bic
  end
end
```

### Placing Validations

This raises the question of where we place our validation method. I see a few possibilities.

If this is a one-off validation, and we don’t need to use it anywhere else, then we can simply include it in the class itself:

```
class CreateBank < Scrivener
  …
  def validate
    …
  end
  
  BIC = /\A[A-Z]{6,6}[A-Z2-9][A-NP-Z0-9]([A-Z0-9]{3,3}){0,1}\z/

  def assert_bic(att, error = [att, :not_bic]
    assert_format att, BIC, error
  end
end
```

If the validation will be used across multiple validators then I see two options. One is to create a sub-class of Scrivener which we can then use as a super-class of all of our validators:

```
class Validator < Scrivener
  BIC = /\A[A-Z]{6,6}[A-Z2-9][A-NP-Z0-9]([A-Z0-9]{3,3}){0,1}\z/

  def assert_bic(att, error = [att, :not_bic]
    assert_format att, BIC, error
  end
end

class CreateBank < Validator
  …
end
```

The other is to use a module and include it as necessary:

```
module CustomValidations
  BIC = /\A[A-Z]{6,6}[A-Z2-9][A-NP-Z0-9]([A-Z0-9]{3,3}){0,1}\z/

  def assert_bic(att, error = [att, :not_bic]
    assert_format att, BIC, error
  end
end

class CreateBank < Scrivener
  include CustomValidations
  
  …
end
```

The module include is actually how Scrivener does it internally.

## SEPA Validation

We can go further and ensure that the BIC that is provided comes from a country in the Single Euro Payments Area (SEPA). 

```
SEPA_COUNTRY_CODES = %w(
  AT BE BG CH CY CZ DE DK EE ES FI 
  FR GB GR HR HU IE IS IT LI LT LU 
  LV MC MT NL NO PL PT RO SE SK SI)
  
def assert_sepa(att, error = [att, :not_sepa])
  sepa_code = send(att)[4,2]
  assert_member sepa_code, SEPA_COUNTRY_CODES
end
```

And if we wanted to tidy things up a little and lift this up to a higher-level (business logic) validation we could combine the BIC and SEPA validations together easily. We’ll prefer to delegate the error handling to the lower-level methods.

```
def assert_supported_bic(att)
  if assert_bic(att)
    assert_sepa(att)
  end
end
```

Since the errors hash is being modified by the validation methods the appropriate validations will be added automatically. 

```
def validate
  assert_present :name
  assert_supported_bic :bic
end
```

This is purely a convenience but it does demonstrate the composability of these validations and the ability, as is common with Ruby, to bring the code in line with the language of your domain.

Scrivener therefore does what it says on the tin. It separates concerns, pulling out validations and filtering into a tidy, transparent Ruby object with wonderfully composable validations.

—*Saturday 27th February 2021.*

[mvs]: https://www.crossingtheruby.com/2021/02/25/validations-with-scrivener.html
[svc]: https://www.crossingtheruby.com/2021/02/26/validations-with-scrivener-2.html
