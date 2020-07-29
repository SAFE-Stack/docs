# Bulma Support on SAFE

[Bulma](https://bulma.io/documentation/) is a free open-source UI framework based on flex-box that helps you create modern and responsive layouts. When it comes to using Bulma as your front-end library on a SAFE Stack web application, you have two options.

1. [Fulma](https://fulma.github.io/Fulma/#fulma): Fulma provides a wrapper around Bulma for fable-react.
1. [Feliz.Bulma](https://zaid-ajaj.github.io/Feliz/#/UIFrameworks/Bulma): Feliz.Bulma is a Bulma wrapper for [Feliz](https://zaid-ajaj.github.io/Feliz/#/).

By adding either of these to your SAFE project alongside the [Bulma stylesheet or the Bulma NPM package](https://bulma.io/documentation/overview/start/), you can take full advantage of Bulma.

### Using Fulma
1. [Add the Fulma NuGet package](#TODO:_INSERT_LINK) to your project
1. [Add Bulma to your project](#TODO:_INSERT_LINK) (as a stylesheet or the npm package)
1. Insert `open Fulma` at the top of your `.fs` files
1. Start using Fulma components in your code.

```fsharp
Button.button [] [
   str "Click me!"
]
```

### Using Feliz.Bulma
1. Add Feliz.Bulma to your project
1. Insert `open Feliz.Bulma` at the top of any of your `.fs` files
1. Start using Feliz.Bulma components in your code

```fsharp
Bulma.button.button [
   str "Click me!"
]
```
