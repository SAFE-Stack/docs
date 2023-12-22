# How do I add Bulma to a SAFE project?

[Bulma](https://bulma.io/documentation/) is a free open-source UI framework based on flex-box that helps you create modern and responsive layouts. When it comes to using Bulma as your front-end library on a SAFE Stack web application, you have two options.

1. [Feliz.Bulma](https://zaid-ajaj.github.io/Feliz/#/UIFrameworks/Bulma): Feliz.Bulma is a Bulma wrapper for [Feliz](https://zaid-ajaj.github.io/Feliz/#/).
1. [Fulma](https://fulma.github.io/Fulma/#fulma): Fulma provides a wrapper around Bulma for fable-react.

By adding either of these to your SAFE project alongside the [Bulma stylesheet or the Bulma NPM package](https://bulma.io/documentation/overview/start/), you can take full advantage of Bulma.

### Using Feliz.Bulma
1. [Add the Feliz.Bulma NuGet package to the solution](../package-management/add-nuget-package-to-client.md).
1. Start using Feliz.Bulma components in your F# files.
```fsharp
open Feliz.Bulma

Bulma.button.button [
   str "Click me!"
]
```

### Using Fulma
1. [Add the Fulma NuGet package to the solution](./../package-management/add-nuget-package-to-client.md).
1. Start using Fulma components in your F# files.
```fsharp
open Fulma

Button.button [] [
   str "Click me!"
]
```