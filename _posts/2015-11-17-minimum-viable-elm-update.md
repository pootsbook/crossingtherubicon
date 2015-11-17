---
layout: post
title: "Minimum Viable Elm: Update"
subtitle: Poots’ Piddling Program
---

Now that we have seen the [view][v] and the [model][m], it’s time to investigate update; the third and last element of Elm’s Model-Update-View Architecture.

Up until this point our application has been static, i.e., there is no way to change the state unless we actually edit the code and recompile. If we’re in this malarkey to build dynamic web applications this isn’t terribly useful—we’re not doing a very good job.

### Application Communication

The update function allows actions to be carried out and to affect the application state (our model) which can then be reflected in the UI (our view). We have previously seen the flow of data in that direction (model -> view) and we will see that update gives us the opportunity to communicate in the other direction (view -> model).

Elm accomplishes the flow of data with Signals; streams of data which our application can react to, or better defined, simply a value that changes over time. The archetypal example is that of mouse position which changes when the user moves the mouse.<sup>1</sup> We don’t necessarily need to know the ins and outs of Signals to get going with Elm as it handily provides a module for us that takes care of all the plumbing and wiring for us: `StartApp.Simple`.

### StartApp.Simple

When we import this, we do need to be aware of what we need to do in our code to satisfy its requirements. There are two modifications we can make to the code we have. 

The first is to provide an `address` argument to the view function. This acts as a target or destination for any actions that we might trigger. StartApp will make sure that it gets routed back to the update function. The second is to define an `update` function, which takes an action and the model. This is what our app looks like with that plumbing added.

    import Html exposing (..)
    import StartApp.Simple as StartApp

    main =
      StartApp.start {
          model = model, view = view, update = update
        }

    type alias Model =
      { greeting : String
      , language : String
      }

    model =
      { greeting = "Hello, "
      , language = "Elm!"
      }
      
      
    update action model =
      model

    view address model = 
      text (model.greeting ++ model.language)


If we compile and run the program (using [Try Elm][try-elm]) then we get exactly the same output as before, but we have laid the foundation for integrating interactivity. 

### Our First Action

In Part 1, I mentioned that for an action we needed two things, a Union type enumerating a set of actions, and an update function with all available actions in a case statement. There’s also a third element, and that is a means of calling the action.

Below the Model type alias we can add the Union type with an action of SayHi (1). After the model function we can add an update function which handles the SayHi action (2). And in the view we can add a trigger (3).

The Html package we have been using comes with support for several browser events like `onclick` and `onsubmit`, effectively allowing us to add event handlers. We’ll need to pull in the `Html.Events` module (4) to access these functions.

Let’s add a button with an `onClick` action that changes the greeting from “Hello” to “Hi”:

    import Html exposing (..)
    import Html.Events exposing (onClick) -- (4)
    import StartApp.Simple as StartApp

    main =
      StartApp.start {
          model = model, view = view, update = update
        }

    type alias Model =
      { greeting : String
      , language : String
      }
      
    type Action =  -- (1)
      SayHi

    model =
      { greeting = "Hello, "
      , language = "Elm!"
      }
      
      
    update action model =
      case action of       -- (2)
        SayHi ->
          { model | 
            greeting <- "Hi, "
          }

    view address model = 
      div [ ] 
        [ text (model.greeting ++ model.language)
        , button [ onClick address SayHi ] [ text "Say Hi!" ] -- (3)
        ]

### Our Second Action
 
The beauty of this approach becomes clear when we see how simple it is to add another action; we only have to repeat ourselves. I’ve numbered the changes as before.

    import Html exposing (..)
    import Html.Events exposing (onClick)
    import StartApp.Simple as StartApp

    main =
      StartApp.start {
          model = model, view = view, update = update
        }

    type alias Model =
      { greeting : String
      , language : String
      }
      
    type Action
      = SayHi
      | SayBye -- (1)

    model =
      { greeting = "Hello, "
      , language = "Elm!"
      }
      
      
    update action model =
      case action of       
        SayHi ->
          { model | 
            greeting <- "Hi, "
          }
          
        SayBye -> -- (2)
          { model |
            greeting <- "Bye, "
          , language <- "JavaScript!"
          }

    view address model = 
      div [ ] 
        [ text (model.greeting ++ model.language)
        , button [ onClick address SayHi ] [ text "Say Hi!" ] 
        , button [ onClick address SayBye ] [ text "Say Bye!" ] -- (3)
        ]

### Conclusion

I hope in these three posts I have been able to communicate some of the simplicity, elegance and beauty of Elm by putting together the most basic application that could work while explaining bits and pieces along the way. What is most attractive to me is that by means of functional composition, i.e. putting these pieces together (Model-Update-View), and also treating the whole as a unit that can also be put together with other units, we don’t get much more complicated than this (conceptually, architecturally) as we scale our application.

To get a feel for the bounds of Elm I heartily recommend working through the [Elm Architecture Tutorial][elm-arch], which Evan Czaplicki put together to demonstrate the flexibility, modularity and simplicity of this approach.

—*Tuesday 17th November 2015*

[elm-arch]: https://github.com/evancz/elm-architecture-tutorial/
[try-elm]: http://elm-lang.org/try

<ol class="footnotes">
<li>
Our main function in this post actually returns a Signal of Html, given that the HTML representation of our application state will change in response to changes in the application state.
</li>
</ol>

[v]: http://crossingtheruby.com/2015/11/11/minimum-viable-elm-view.html
[m]: http://crossingtheruby.com/2015/11/12/minimum-viable-elm-model.html
