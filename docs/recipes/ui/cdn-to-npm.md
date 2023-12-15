# How do I migrate from a CDN stylesheet to an NPM package?
Often, referencing a stylesheet from a CDN is all that's needed to add new styles but you can use an NPM package instead.
---

#### 1. Remove the CDN Reference
Remove the CDN reference from the index template in `src/Client/index.html`:
```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.9.0/css/bulma.min.css">
```

#### 2. Add the NPM Package
Add styles from NPM. [How do I add an NPM package to the client?](../../package-management/add-npm-package-to-client)  
In this example we will add the [Bulma NPM package](https://www.npmjs.com/package/bulma).

#### 3. Add a reference to your stylesheet
1. Add a stylesheet to your project using [this recipe](../add-style). Add a scss file instead of a css file.
1. Add the following lines to your scss file:
    ```scss
    // Set variables to affect Bulma styles
    $body-color: #c6538c;
    @import 'bulma/bulma.sass';
    ```
