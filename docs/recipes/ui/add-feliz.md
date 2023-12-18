# How do I add Feliz to a SAFE project?
[Feliz](https://github.com/Zaid-Ajaj/Feliz) is a wrapper for the base [React](https://reactjs.org/) DSL library that emphasises consistency, lightweight formatting, discoverable attributes and full type-safety. The default SAFE Template already uses Feliz.

### Using Feliz
1. [Add Feliz to your project](./../../v4-recipes//package-management/add-nuget-package-to-client.md)
1. Start using Feliz in your code.

```fsharp
open Feliz

Html.button [
    prop.style [ style.marginLeft 5 ]
    prop.onClick (fun _ -> setCount(count - 1))
    prop.text "Decrement"
]
```

