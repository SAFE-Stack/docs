# How do I add Tailwind to a SAFE project?

[Tailwind](https://tailwindcss.com/) is a utility-first CSS framework which can be composed to build any design, directly in your markup.

As of SAFE version 5 (released in December 2023) it is included in the template by default so it can be used straight away.

If you are are using the minimal template or if you are upgrading from an old version of SAFE, continue reading for installation instructions.

1. [Add a stylesheet](https://safe-stack.github.io/docs/recipes/ui/add-style/) to the project

1. Install the required npm packages
    ```shell
    npm install -D tailwindcss postcss autoprefixer
    ```
1. Initialise a `tailwind.config.js`
    ```shell
    npx tailwindcss init
    ```
1. Amend the `tailwind.config.js` as follows
    ```javascript
    /** @type {import('tailwindcss').Config} */
    module.exports = {
      mode: "jit",
      content: [
        "./index.html",
        "./**/*.{fs,js,ts,jsx,tsx}",
      ],
      theme: {
        extend: {},
      },
      plugins: [],
    }
    ```

1. Create a `postcss.config.js` with the following
    ```javascript
    module.exports = {
      plugins: {
        tailwindcss: {},
        autoprefixer: {},
      }
    }
    ```

1. Add the Tailwind layers to your stylesheet
    ```css
    @tailwind base;
    @tailwind components;
    @tailwind utilities;
    ```

1. Start using tailwind classes e.g.
    ```fsharp
    for todo in model.Todos do
        Html.li [
            prop.classes [ "text-red-200" ]
            prop.text todo.Description
        ]
    ```