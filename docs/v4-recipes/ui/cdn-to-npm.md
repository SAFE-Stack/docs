# How do I migrate from a CDN stylesheet to an NPM package?
Though the SAFE template default for referencing a stylesheet is to use a CDN, it’s quite reasonable to want to use an NPM package instead. One common case is that it enables you to further customise Bulma themes by overriding Sass variables.

---

#### 1. Remove the CDN Reference
Find the following line in `src/Client/index.html` and delete it before moving on:
```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.9.0/css/bulma.min.css">
```

#### 2. Add the NPM Package
Go ahead and add the [Bulma NPM package](https://www.npmjs.com/package/bulma) to your project.
> See: [How do I add an NPM package to the client?](../package-management/add-npm-package-to-client.md)

#### 3. Load the Stylesheets
There are two ways for loading the stylesheets:

##### Fable Interop
A quick and easy way to reference this NPM package in an F# file is to insert the following couple of lines:

```fsharp
open Fable.Core.JsInterop
importAll "bulma/bulma.sass"
```

> You can use this approach for any NPM package.

##### b. Using Sass
1. Add a Sass stylesheet to your project using [this recipe](add-style.md).
2. Add the following line to your Sass file to bring in Bulma
```sass
@import "~bulma/bulma.sass"
```
