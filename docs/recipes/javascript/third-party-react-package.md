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
import ReactSpeedometer from "react-d3-speedometer"
```
The reason for using `importDefault` is the documentation for the component uses a default export "ReactSpeedometer". Please find a list of common import statetments at the end of this recipe

As a quick check to ensure that the library is being imported and we have no typos you can `console.log` the following at the top within the view function 
```fsharp
Browser.Dom.console.log("REACT-D3-IMPORT", importDefault "react-d3-speedometer")
```
In the console window which can be reached right-clicking and selecting Insepct Element, you should see some output from the above log.

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

That's the bare minimum needed to get going!

## Next steps

Once your component is working you may want to extract out the logic so that it can be used in multiple pages of your app.
A full detailed tutorial is in the works!

## How to handle different types of import statements 

### Default export

In most cases components use the default export syntax which is when the the component being exported from the module becomes available. For example, if the module being imported below looked something like:
```javascript
const foo = () => "hello"

export default foo
```
We can use the below syntax to have access to the function `foo`.
```javascript
import foo from 'module-name' // JS
```
```fsharp
importDefault "module-name" // F#
``` 

### Named export 

In some cases components can use the named export syntax. In the below case "module-name" has an object/function/class that is called `bar`. By referncing it below it is brought into the current scope. 
For example, if the module below contained something like:
```javascript 
export const bar (x,y) => x + y 
```
We can directly access the function with the below syntax 
```javascript 
import { bar } from "module-name" // JS
```
```fsharp
import "bar" "module-name" // F#
```
### Entire module contents 

In rare cases you may have to import an entire module's contents and provide an alias in the below case we named it myModule. You can now use dot notation to access anything that is exported from module-name. For example, if the module being imported below includes an export to a function `doAllTheAmazingThings()` you could access it like:
```javascript
myModule.doAllTheAmazingThings()
```
```javascript
import * as myModule from 'module-name' // JS
```
```fsharp
let myModule = importAll "module-name" // F#
``` 

