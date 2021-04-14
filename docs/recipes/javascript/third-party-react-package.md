# How do I use a third party React package with Feliz?

With Feliz we're able to easily interact with third-party React libraries in a SAFE application.

## Prerequisites

This recipe uses the [react-d3-speedometer NPM package](https://www.npmjs.com/package/react-d3-speedometer) for demonstration purposes. [Add it to your Client](../../package-management/add-npm-package-to-client) before continuing.

If you don't already have [Feliz](https://www.nuget.org/packages/Feliz/) installed, [add it to your client](../../ui/add-feliz).

## Setup

In the Client projects `Index.fs` add the following snippets

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

- `createElement` from `Feliz.ReactApi.IReactApi` takes the component you're wrapping react-d3-speedometer, the props that component takes and creates a ReactComponent we can use in F#.
- `importDefault` from ` Fable.Core.JsInterop` is giving us access to the component and is equivalent to 
```javascript 
import defaultExport from "react-d3-speedometer"
```
- `createObj` from `Fable.Core.JsInterop` takes a sequence of `string * obj` which is a prop name and value for the component, you can find the full prop list for react-d3-speedometer [here](https://www.npmjs.com/package/react-d3-speedometer).
- Using `==>` (short hand for `prop.custom`) to transform the sequence into a javascript object 
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