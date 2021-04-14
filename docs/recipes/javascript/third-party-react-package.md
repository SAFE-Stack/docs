# How Do I Use a Third Party React Package Using Feliz?

To use a third-party React library in a SAFE application, you need to write an F# wrapper around it. There are two ways for doing this – using [Fable.React](https://www.nuget.org/packages/Fable.React/) or using [Feliz](https://zaid-ajaj.github.io/Feliz/).
This recipe will show you the minimum setup needed to get going!

## Prerequisites

This recipe uses the [react-d3-speedometer NPM package](https://www.npmjs.com/package/react-d3-speedometer) for demonstration purposes. [Add it to your Client](../../package-management/add-npm-package-to-client) before continuing.

If you don't already have [Feliz](https://www.nuget.org/packages/Feliz/) installed, [add it to your client](../../ui/add-feliz).

## Minimum

In the Client project's `Index.fs` add the following snippets

```fsharp
open Fable.Core.JsInterop
```

 Within the view function 
```fsharp 
Feliz.Interop.reactApi.createElement (importDefault "react-d3-speedometer", createObj [
            "minValue" ==> 0
            "maxValue" ==> 100
            "value" ==> 10
        ])
```

- `createElement` from `Feliz.ReactApi.IReactApi.createElement` takes the component you're wrapping in our case react-d3-speedometer and the props for that component.
- `importDefault` from ` Fable.Core.JsInterop.importDefault` is giving us access to the component and is equivalent to 
```javascript 
import defaultExport from "react-d3-speedometer"
```
- createObj from `Fable.Core.JsInterop.createObj` takes a sequence of `string * obj` using the `==>` (this is short hand for `prop.custom`) to transform the sequence into a javascript object 
```fsharp
createObj [
    "minValue" ==> 0
    "maxValue" ==> 10
]
```
Is equivalent to 
```javascript 
{ minValue: 0, maxValue: 10 }
```

That is the bare minimum needed to get going!
