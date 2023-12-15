# How do I use stylesheets with SAFE?
The default way to add extra styles is to add them using Tailwind classes.
If you wish to use your own CSS stylesheets with SAFE apps, Vite can bundle them up for you.

There are two different approaches to adding your own CSS file, depending on what files you have available.

## Method A: Import into `index.css`
The default template includes a stylesheet at  `src/Client/index.css` which contains references to Tailwind.
The cleanest way to add your own stylesheet is to create a new file e.g. `src/Client/custom-style.css` and then reference it from `index.css`.

1. Create your custom css file in `src/Client`, e.g. `custom-style.css`
1. Import it into `index.css`
    ```diff
    +@import "./custom-style.css";
     @tailwind base;
     @tailwind components;
     @tailwind utilities;
    ```

## Method B: Import without `index.css`
In order for Vite to know that there are styles to be bundled, you must import them into your app. By default this is already configured for `index.css` but if you don't have it set up, not to worry! Follow these steps:

1. Create your custom css file in `src/Client`, e.g. `custom-style.css`
1. Direct Fable to emit an import for your style file.
    - Add the following to `App.fs`:
        ```fsharp
        open Fable.Core.JsInterop
        importSideEffects "./custom-style.css"
        ```

## There you have it!
You can now style your app by writing to the `custom-style.css` file.