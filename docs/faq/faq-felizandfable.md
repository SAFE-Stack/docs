In a nutshell Fable.React and Feliz are two F# libraries which perform a similar function:

* Provide a typesafe F# wrapper to allow you to interact with React.
* Provide a way to create your own wrappers around existing React components.
* Provide a DSL for you to consume and create React wrappers in a consistent way.

## DSL differences
The main distinction between the two libraries is that Fable.React follows a layout as follows:

```fsharp
element [ attribute; attribute ] [ childElement; childElement ]
```

> Note: Code snippets below are for illustrative purposes only and do not compile.

For example:

```fsharp
h1 [ style "color:Tomato" ] [
    p [] [ text "Hello" ] // no attributes
    p [] [ text "Another paragraph" ] // no attributes
    h2 [ style "color:Blue" ] [] // no child elements
]
```

In this example, `h1` is the "top level" element, with a single attribute and three children, two of which have their own children.

Feliz adopts a different style, in which instead of an element having two lists, there is only one. The list directly contains *either* attributes *or* child elements, but not both:

The above snippet would convert into Feliz as follows:

```fsharp
h1 [
    style "color:Tomato"
    children [
        p [ text "Hello" ]
        p [ text "Another paragraph" ]
        h2 [ style "color:Blue" ]
    ]
]
```

The `children` function is required when mixing and matching attributes and elements:

```fsharp
h1 [ // this is fine - just attributes underneath h1
    style "color:Tomato"
    title "foo"
]

h1 [  // fine - just elements underneath h1
    p [ text "Hello" ]
]

h1 [  // not fine - can't mix and match attributes and elements
    style "color:Tomato"
    p [ text "Hello" ]
]
```

In order to allow both attributes and elements in the same list, Feliz introduces the `children` node:

```fsharp
h1 [ // this is now fine
    style "color:Tomato"
    children [
        p [ text "Hello" ]
    ]
]
```

## Guidance
* Fable.React was created initially, whilst Feliz was developed some time later.
* Feliz has better support for React interop and the majority of the community nowadays uses the Feliz DSL style for developing components.
* As they are both wrappers around the same underlying technology (React) and Feliz uses some parts of Fable.React, you can actually mix and match the two in your applications as required.