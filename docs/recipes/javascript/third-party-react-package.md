To use a third-party React library in a SAFE application, you need to write an F# wrapper around it. There are two ways for doing this - using [Fable.React](https://www.nuget.org/packages/Fable.React/) or using [Feliz](https://zaid-ajaj.github.io/Feliz/).
## Prerequisites

This recipe uses the [react-d3-speedometer NPM package](https://www.npmjs.com/package/react-d3-speedometer) for demonstration purposes. [Add it to your Client](../package-management/add-npm-package-to-client.md) before continuing.

## Fable.React - Setup

#### 1. Create a new file

Create an empty file named `ReactSpeedometer.fs` in the Client project above `Index.fs` and insert the following statements at the beginning of the file.

```fsharp
module ReactSpeedometer

open Fable.Core
open Fable.Core.JsInterop
open Fable.React
```

#### 2. Define the Props
Prop represents the props of the React component. In this recipe, we're using [the props listed here](https://www.npmjs.com/package/react-d3-speedometer) for `react-d3-speedometer`. We model them in Fable.React using a discriminated union.

```fsharp
type Prop =
    | Value of int
    | MinValue of int
    | MaxValue of int 
    | StartColor of string
```

> One difference to note is that we use **P**ascalCase rather than **c**amelCase.
>
> Note that we can model any props here, both simple values and "event handler"-style ones.

#### 3. Write the Component
Add the following function to the file. Note that the last argument passed into the `ofImport` function is a list of `ReactElements` to be used as children of the react component. In this case, we are passing an empty list since the component doesn't have children.

```fsharp
let reactSpeedometer (props : Prop list) : ReactElement =
    let propsObject = keyValueList CaseRules.LowerFirst props // converts Props to JS object
    ofImport "default" "react-d3-speedometer" propsObject [] // import the default function/object from react-d3-speedometer
```

#### 4. Use the Component
With all these in place, you can use the React element in your client like so:

```fsharp
open ReactSpeedometer

reactSpeedometer [
    Prop.Value 10 // Since Value is already decalred in HTMLAttr you can use Prop.Value to tell the F# compiler its of type Prop and not HTMLAttr
    MaxValue 100
    MinValue 0 
    StartColor "red"
    ]
```

## Feliz - Setup

If you don't already have [Feliz](https://www.nuget.org/packages/Feliz/) installed, [add it to your client](../../v4-recipes/ui/add-feliz.md).
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
import ReactSpeedometer from "react-d3-speedometer"
```
The reason for using `importDefault` is the documentation for the component uses a default export "ReactSpeedometer". Please find a list of common import statetments at the end of this recipe

As a quick check to ensure that the library is being imported and we have no typos you can `console.log` the following at the top within the view function 
```fsharp
Browser.Dom.console.log("REACT-D3-IMPORT", importDefault "react-d3-speedometer")
```
In the console window (which can be reached by right-clicking and selecting Insepct Element) you should see some output from the above log. 
If nothing is being seen you may need a slightly different import statement, [please refer to this recipe](../../v4-recipes/javascript/import-js-module.md).

- `createObj` from `Fable.Core.JsInterop` takes a sequence of `string * obj` which is a prop name and value for the component, you can find the full prop list for react-d3-speedometer [here](https://www.npmjs.com/package/react-d3-speedometer).
- Using `==>` (short hand for `prop.custom`) to transform the sequence into a JavaScript object 

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

That's the bare minimum needed to get going!

#### Next steps for Feliz

Once your component is working you may want to extract out the logic so that it can be used in multiple pages of your app.
For a full detailed tutorial head over to [this blog post](https://www.compositional-it.com/news-blog/f-wrappers-for-react-components/)!



