In a nutshell Fable.React and Feliz are two F# libraries which perform a similar function:

* Provide a typesafe F# wrapper to allow you to interact with React.
* Provide a way to create your own wrappers around existing React components.
* Provide a DSL for you to consume and create React wrappers in a consistent way.

## DSL differences
The main distinction between the two libraries is that Fable.React follows a layout as follows:

```fsharp
element [ prop; prop ] [ childElement; childElement ]
```

For example:

```fsharp
h1 [ Style [ Color "Tomato" ] ] [
    p [] [ str "Hello" ] // no props
    p [] [ str "Another paragraph" ] // no props
    h2 [ Style [ Color "Blue" ] ] [] // no child elements
]
```

In this example, `h1` is the "top level" element, with a single attribute and three children, two of which have their own children.

Feliz adopts a different style, in which instead of an element having two lists, there is only one. The list directly contains *either* attributes *or* child elements, but not both:

The above snippet would convert into Feliz as follows:

```fsharp
Html.h1 [
    prop.style [ style.color "Tomato" ]
    prop.children [
        Html.p [ prop.text "Hello" ]
        Html.p "Another paragraph"
        Html.h2 [ prop.style [ style.backgroundColor "Blue" ] ]
    ]
]
```

The `prop.children` function is required when mixing and matching attributes and elements:

```fsharp
Html.h1 [ // this is fine - just props underneath h1
    prop.style [ style.color "Tomato" ]
    prop.title "foo"
]

Html.h1 [ // fine - just elements underneath h1
    Html.p [ prop.text "Hello" ]
]

Html.h1 [ // not fine - can't mix and match attributes and elements
    prop.style [ style.color "Tomato" ]
    Html.p [ prop.text "Hello" ]
]

Html.h1 [ // fine, adding props, and adding children using prop.children
    prop.style [ style.color "Tomato" ]
    prop.children [ Html.p [ prop.text "Hello" ] ]
]
```

## Guidance
* Fable.React was created initially, whilst Feliz was developed some time later.
* Feliz has better support for React interop and the majority of the community nowadays uses the Feliz DSL style for developing components.
* Fable.React and Feliz can be mixed into your application if required (for progressive migration for example)


* Also see [My journey with Feliz | A comparison between Fable.React and Feliz](https://github.com/Zaid-Ajaj/Feliz/issues/155).