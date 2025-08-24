# How do I import a JavaScript module?

Sometimes you need to use a JS library directly, instead of using it through a wrapper library that makes it easy to use from F# code. In this case you need to import a module from the library.
Here are the most common import patterns used in JS.

### Default export

#### Setup

In most cases components use the default export syntax which is when the the component being exported from the module becomes available. For example, if the module being imported below looked something like:
```javascript 
// module-name
const foo = () => "hello"

export default foo
```
We can use the below syntax to have access to the function `foo`.
```javascript
import foo from 'module-name' // JS
```
```fsharp
let foo = importDefault "module-name" // F#
``` 

#### Testing the import 

To ensure that the import was successful, you can console log the value and you should see the value in the browser's console window, which you can get to by right-clicking and selecting the Inspect Element.
```fsharp
Browser.Dom.console.log("imported value", foo)
```

#### Example

An example of this in use is how React is imported
```javascript 
import React from "react"

// Although in the newer versions of React this is uneeded
```

### Named export 

#### Setup

In some cases components can use the named export syntax. In the below case "module-name" has an object/function/class that is called `bar`. By referencing it below, it is brought into the current scope. 
For example, if the module below contained something like:
```javascript 
export const bar (x,y) => x + y 
```
We can directly access the function with the below syntax 
```javascript 
import { bar } from "module-name" // JS
```
```fsharp
let bar = import "bar" "module-name" // F#
```
#### Testing the import 

To ensure that the import was successful, you can console log the value and you should see the value in the browser's console window, which you can get to by right-clicking and selecting the Inspect Element.
```fsharp
Browser.Dom.console.log("imported value", bar)
```

#### Example 

An example of this is how React hooks are imported
```javascript 
import { useState } from "react"
```
### Entire module contents 

In rare cases you may have to import an entire module's contents and provide an alias. In the below case we named it myModule. You can now use dot notation to access anything that is exported from module-name. For example, if the module being imported below includes an export to a function `doAllTheAmazingThings()` you could access it like:
```javascript
myModule.doAllTheAmazingThings()
```
```javascript
import * as myModule from 'module-name' // JS
```
```fsharp
let myModule = importAll "module-name" // F#
``` 

#### Testing the import 

To ensure that the import was successful, you can console log the value and you should see the value in the browser's console window, which you can get to by right-clicking and selecting the Inspect Element.
```fsharp
Browser.Dom.console.log("imported value", myModule)
```

#### Example

An example of this is another way to import React 
```javascript 
import * as React from "react"

// Uncommon since importDefault is the standard
```

### More information

See the [Fable docs](https://fable.io/docs/communicate/js-from-fable.html) for more ways to import modules and use JavaScript from Fable.
