# How do I add Tailwind to a SAFE project?

[Tailwind](https://tailwindcss.com/) is a utility-first CSS framework packed that can be composed to build any design, directly in your markup.

1. [Add a stylesheet](https://safe-stack.github.io/docs/recipes/ui/add-style/) to the project

2. Install the required npm packages
```shell
npm install -D tailwindcss postcss autoprefixer postcss-loader
```
3. Initialise a `tailwind.config.js`
```shell
npx tailwindcss init
```
4. Amend the content array in the `tailwind.config.js` as follows
```javascript
module.exports = {
  content: [
      './src/Client/**/*.html',
      './src/Client/**/*.fs',
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

5. Create a `postcss.config.js` with the following
```javascript
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  }
}
```

6. Add the Tailwind layers to your `style.css`

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

7. Find the `module.rules` field in the `webpack.config.js` and in the css files rule’s `use` field add `postcss-loader`

```javascript
{
    test: /\.(sass|scss|css)$/,
    use: [
        isProduction
            ? MiniCssExtractPlugin.loader
            : 'style-loader',
        'css-loader',
        {
            loader: 'sass-loader',
            options: { implementation: require('sass') }
        },
        'postcss-loader'
    ],
},
```

8. In the `src/Client` folder find the code in `Index.fs` to show the list of todos and add a Tailwind text colour class(text-red-200)

```fsharp
for todo in model.Todos do
    Html.li [
        prop.classes [ "text-red-200" ]
        prop.text todo.Description
    ]
```

You should see some nice red todos proving that Tailwind is now in your project