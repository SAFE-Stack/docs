# How do I add Bulma to a SAFE project?

[Bulma](https://bulma.io/documentation/) is a free open-source UI framework based on flex-box that helps you create modern and responsive layouts.

When using Feliz (the standard for a SAFE app), follow the instructions below. When using Fable.React, use the [Fulma](https://fulma.github.io/Fulma/) wrapper for Bulma.

## 1. Add the Feliz.Bulma NuGet package to the solution

```
dotnet paket add Feliz.Bulma -p Client
```

## 2. Add the Bulma stylesheet to `index.html`

```.diff
 ...
 <head>
     ...
+    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.9.4/css/bulma.min.css">
 </head>
 ...
```

## 3. Start using Feliz.Bulma components in your F# files.

```fsharp
open Feliz.Bulma

Bulma.button.button [
   str "Click me!"
]
```