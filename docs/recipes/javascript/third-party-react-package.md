# How Do I Use a Third Party React Package?

To use a third-party React library in a SAFE application, you need to write an F# wrapper around it. There are two ways for doing this – using [Fable.React](https://www.nuget.org/packages/Fable.React/) or using [Feliz](https://zaid-ajaj.github.io/Feliz/).

## Prerequisites

This recipe uses [react-number-format NPM package](https://www.npmjs.com/package/react-number-format) for demonstration purposes. [Add it to your Client](../../package-management/add-npm-package-to-client) before continuing.

## Using Fable.React

#### 1. Create a new file

Create an empty file named `NumberFormat.fs` in the Client project above `Index.fs` and insert the following statements at the beginning of the file.

```fsharp
module NumberFormat

open Fable.Core
open Fable.Core.JsInterop
open Fable.React
```

#### 2. Define the Props
Props represent the props of the React component. In this recipe, we're using [the props listed here](https://github.com/s-yadav/react-number-format#Props) for `react-number-format`. We model them in Fable.React using a discriminated union.

```fsharp
type Prop =
    | Value of float
    | ThousandSeparator of char
    | OnValueChange of ({| value: string; floatValue : float Option |} -> unit)
```

> One difference to not is that we use **P**ascalCase rather than **c**amelCase.
>
> Note that we can model any props here, both simple values and "event handler"-style ones.

#### 3. Write the Component
Add the following function to the file. Note that the last argument passed into the `ofImport` function is a list of `ReactElements` to be used as *children* of the react component. In this case, we are passing an empty list since we are essentially generating an `input` HTML element that typically doesn't have children.

```fsharp
let numberFormat (props : Prop list) : ReactElement =
    let propsObject = keyValueList CaseRules.LowerFirst props // converts Props to JS object
    ofImport "default" "react-number-format" propsObject [] // import the default function/object from react-number-format
```

#### 4. Use the Component
With all these in place, you can use the React element in your client like so:

```fsharp
open NumberFormat

numberFormat [
    Value 123.
    OnValueChange (fun x -> x.floatValue |> ValueChanged |> dispatch)
    ThousandSeparator ','
]
```

## Using Feliz

#### 1. Add Feliz

If you don't already have [Feliz](https://www.nuget.org/packages/Feliz/) installed, [add it to your client](../../ui/add-feliz).

#### 2. Create a New File

Create a new file named `NumberFormat.fs` at the client just above `Index.fs`, remove its content and insert the following *open* statements at the beginning of the file.

```fsharp
module NumberFormat

open Fable.Core.JsInterop
open Feliz
```

#### 3. Wrap the Component

Insert the following line after the *open* statements. This esentially creates a wrapper around the default object/function of the `react-number-format` package.

```fsharp
let numberFormat : obj = import "default" "react-number-format"
```

#### 4. Abstract the Component

Add the following type to the file. Each of the static members below corresponds to a prop for `react-number-format` [listed here](https://github.com/s-yadav/react-number-format#Props). **The last member is an exception to this** and it will instead allow us to call the React component. The `prop.custom` function creates a JavaScript prop from a key-value pair that we pass to it in the form of a tuple.

```fsharp
type NumberFormat =
    static member inline value (number: float option) = prop.custom ("value", number)
    static member inline onValueChange (data: {| value: string; floatValue : float Option |} -> unit) =
        prop.custom ("onValueChange", data)
    static member inline thousandSeparator (char: char) = prop.custom ("thousandSeparator", char)

    static member inline input props = Interop.reactApi.createElement (numberFormat, createObj !!props)
```

#### 5. Use the Component

With all these in place, you can use the React element in your client like so:

```fsharp
open NumberFormat

div [] [
    NumberFormat.input [
        NumberFormat.value model.Number
        NumberFormat.onValueChange (fun x -> x.floatValue |> ValueChanged |> dispatch)
        NumberFormat.thousandSeparator ','
    ]
]
```