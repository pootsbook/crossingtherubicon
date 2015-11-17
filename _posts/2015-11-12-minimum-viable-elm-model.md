---
layout: post
title: 'Minimum Viable Elm: Model'
subtitle: Poots’ Piddling Program
tags:
  - Elm
---

Yesterday we built the smallest possible Elm application, consisting only of a view layer. This is one third of the Elm Architecture (Model, Update, View) and today we examine the model.

If we take what we did previously, displaying “Hello, Elm!” in the browser, we can modify the code slightly to introduce a model. In this case our data will be the string “Hello, Elm!”.

    import Html exposing (..)
    
    model = 
      "Hello, Elm!"
      
    view = 
      text model
    
    main =
      view

There are a couple of improvements we can make if we look forward to what a more complete application might look like. We can add a type alias for the model, and we can pass the model into the view function, instead of calling it directly.

    import Html exposing (..)
    
    type alias Model = String
    
    model = 
      "Hello, Elm!"
      
    view model = 
      text model
    
    main =
      view model

### Modeling with the Record Type

A string won’t get us very far when we have more data to model in our application, so typically the model will be of type Record. A Record bears a passing resemblance to a JavaScript Object but is intended for a much narrower use, i.e. not everywhere. A Record has fields and values, and each field/value pair can be of a different type. Let’s break our string down:


    import Html exposing (..)

    type alias Model =
      { greeting : String
      , language : String
      }

    model =
      { greeting = "Hello, "
      , language = "Elm!"
      }

    view model = 
      text (model.greeting ++ model.language)

    main =
      view model

Note how we declare the fields and their types in the type alias, and then how we declare the field values in the model function. The model gives us a place to put all of our application state and is the central ‘brain’ for our application and the Elm Architecture.

It isn’t very useful if it remains static, so tomorrow we can take a look at introducing Update in addition to View and Model to complete the triad and make our application interactive.

—*Thursday 12th November 2015*
