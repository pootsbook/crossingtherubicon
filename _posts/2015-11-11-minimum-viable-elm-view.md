---
layout: post
title: 'Minimum Viable Elm: View'
subtitle: Poots’ Piddling Program
---

Inherent in the Elm language are the strict constraints placed on programs by the features immutibility and type inference. These naturally derive what is called The Elm Architecture, consisting of three parts:

1. **Model**  
Your application state, usually in the form of an Elm Record.
2. **Update**  
A way to update the state of your application based on actions triggered by anything outside the application, e.g. a user, a server, an input device. This takes the form of:
* a Union Type enumerating a set of actions;
* an update function containing a case statement with each of the actions in a branch. The encased logic will likely update (or rather, replace) the current model.
3. **View**  
A stateless function that reacts to changes in the model and renders the result, usually, but not limited to, HTML.

Let’s work our way up to the Counter example, which is the starting point for the Elm Architecture Tutorial. In this post we start with the View.

### Hello Elm!

Technically we don’t *need* an interactive application if all we want to do is display something on the screen (view). That means we can leave out our state (model) and any interactivity (update). Let’s make a simple application that displays the text “Hello Elm!”.

We declare a `main` function that renders a string to an HTML document as a text node. For this we will need to import the text function from the Html package:

    import Html exposing (text)
    
    main =
      text "Hello Elm!"

Have a go yourself with the [Elm Online Editor][online-editor]. Type the code in and hit the ‘Compile’ button. You’ve written your first Elm program!

Note that the `main` function is picked up by the Elm runtime as the entry point to your application and it kicks everything off.

### Elm HTML

A text node works, but we can move onto HTML elements. Let’s put our text in a `<div>`.

    import Html exposing (text, div)

    main =
      div [ ] [ text "Hello Elm!" ]

We’ve had to expand our import statement to include the `div` function and our `div` function (like all element node functions) takes two arguments, both of type **List**. The first list, which is empty in our case, is an attribute list, of type **Attribute**. The second argument is a list of children of type **Html**. The function then returns something of type **Html**. Elm uses Type Annotations, so the type annotation for `div` looks like this:

    div : List Attribute -> List Html -> Html

which is a much more concise way of what I wrote above in long hand.

### Nesting

The following illustrates a fuller example with a more nested structure:

    import Html exposing (..)
    import Html.Attributes exposing (class, href)

    main =
      div [ class "content" ] [
        p [ ]
        [ text "Check this site out: "
        , a [ href "http://elm-lang.org/" ] [ text "Elm" ]
        ]
      ]

We’ve opened up the `Html` import to include all functions with `..` and pulled in the `Html.Attributes` module to give us access to the `class` and `href` functions. If you have used the Haml or Jade templating engines before, you’ll spot the resemblance in syntax and structure.

### Composition

What’s really nice is that we can start to break up that example into smaller parts and compose it back into the whole. This is where the functional nature of Elm allows us to do with our ‘template’ what we do with our code—because it is code.

    import Html exposing (..)
    import Html.Attributes exposing (..)
    
    linkToElm =
      a [ href "http://elm-lang.org/" ] [ text "Elm" ]
    
    content link =
      p [ ]
        [ text "Check this site out: "
        , link
        ]
    
    main =
      div [ class "content" ] [ content linkToElm ]

Although this example is a little contrived, you can see how easy it would be to break up more complex sections of UI to keep things neat and tidy.

Tomorrow we’ll take a look at integrating the Model into what we’ve done today with the View.

—*Wednesday 11th November 2015*

[online-editor]: http://elm-lang.org/try
