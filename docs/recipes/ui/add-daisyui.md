
[DaisyUI](https://daisyui.com/) is a component library for [Tailwind CSS](https://tailwindcss.com/docs/installation).  
To use the library from within F# we will use [Feliz.DaisyUI](https://dzoukr.github.io/Feliz.DaisyUI/) ([Github](https://github.com/Dzoukr/Feliz.DaisyUI)).

1. Follow the instructions for how to [add Tailwind CSS to your project](../add-tailwind/)

1. Add daisyUI JS dependencies using NPM: `npm i -D daisyui@latest`

1. Add Feliz.DaisyUI .NET dependency...
    - via Paket:  `dotnet paket add Feliz.DaisyUI`
    - via NuGet: `dotnet add package Feliz.DaisyUI`

1. Update the `tailwind.config.js` file's `module.exports.plugins` array; add `require("daisyui")`

    ```{ .js title=tailwind.config.js hl_lines=10 }
    module.exports = {
        content: [
            './src/Client/**/*.html',
            './src/Client/**/*.fs',
        ],
        theme: {
            extend: {},
        },
        plugins: [
            require("daisyui"),
        ],
    }
    ```

1. Open the daisyUI namespace wherever you want to use it.
    ```{ .fsharp title=YourFileHere.fs }
    open Feliz.DaisyUI
    ```

1. Congratulations, now you can use daisyUI components!  
    Documentation can be found at [https://dzoukr.github.io/Feliz.DaisyUI/](https://dzoukr.github.io/Feliz.DaisyUI/#/)
