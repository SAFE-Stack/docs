# How do I import a JavaScript module?

Sometimes you need to use a JS library directly, instead of using it through a wrapper library that makes it easy to use from F# code. In this case you need to import a module from the library.

We will use the `react` module from the React NPM package as an example here because it is always included as an NPM dependency in SAFE projects.

### Importing everything from a module

In your client code, use the `importAll` function to import all items from a given module name.

```fsharp
open Fable.Core.JsInterop

let react : obj = importAll "react"

```

Now you can access items contained in the module using the dynamic JS interop provided by Fable. This example just prints the version of React being used.

```fsharp
printfn "React version: %s" react?version
```

### Import one value from a module

To import a single value use the `import` function instead. You need to provide both the name of the value and the module name.

```fsharp
open Fable.Core.JsInterop

let reactVersion : string = import "version" "react"
```

Now you can use this value directly.

```fsharp
printfn "React version: %s" reactVersion
```

### More information

See the [Fable docs](https://fable.io/docs/communicate/js-from-fable.html) for more ways to import modules and use JavaScript from Fable.
