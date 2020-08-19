# Feliz Support on SAFE
[Feliz](https://github.com/Zaid-Ajaj/Feliz) is a wrapper for the base Reach DSL library that emphasises consistency, lightweight formatting, discoverable attributes and full type-safety. It is quick and easy to add Feliz to your SAFE application and start using it.

### Using Feliz
1. [Add Feliz to your project](./../package-management/add-nuget-package-to-client.md)
1. Start using Feliz in your code.

```fsharp
open Feliz

Html.button [
    prop.style [ style.marginLeft 5 ]
    prop.onClick (fun _ -> setCount(count - 1))
    prop.text "Decrement"
]
```

