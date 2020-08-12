# How Do I Use SASS with SAFE?
[Sass](https://sass-lang.com) is a stylesheet language that’s compiled to CSS. Using Sass with SAFE apps will allow you to use variables, nested rules, mixins, functions, and more in your stylesheets.
There are two different syntax options to use with Sass – the *indented syntax* and the *SCSS* syntax. This recipe uses SCSS, however the setup instructions will be valid no matter which one you prefer. For more information on the matter, [click here](https://sass-lang.com/documentation/syntax).

## Prerequisites
Whether you’re using the standard template or the minimal, you will need to install the following NPM packages to your solution:
1. [css-loader](https://www.npmjs.com/package/css-loader) – Interprets `@import` and `url()` like `import`/`require()` and will resolve them
2. [style-loader](https://www.npmjs.com/package/style-loader) – Inject CSS into the DOM.
3. [sass](https://www.npmjs.com/package/sass) – A pure JavaScript implementation of  Sass.
4. [sass-loader](https://www.npmjs.com/package/sass-loader) – Loads a Sass/SCSS file and compiles it to CSS.

> [Click here](../package-management/add-npm-package-to-client.md) to see the recipe for adding NPM packages to client.

## Adding the Stylesheet
Then, you will need to create an SCSS file in the `src/Client` folder of your solution e.g `style.scss`. You might also want the stylesheet to be “included” in the `Client.fsproj` file in order for it to be seen in the *solution view* of your IDE. This is not essential, but recommended.

- - - -
The following steps will vary depending on whether you’re using the standard template or the minimal one. Follow the steps that corresponds to the version of the SAFE template you’re using and ignore the rest.  
- - - -

## I am Using the Standard Template
1. Navigate to the `src/Client` folder and open the `webpack.config.js` file.
2. Find the `CONFIG` module and add the following variable if it doesn’t already exist.
    ```javascript
    sassEntry: './style.scss',
    ```
4. Find the `entry` field in the  `module.exports` object at the bottom of the file, and replace it with the following:
    ```javascript
    entry: isProduction ? {
       app: [resolve(CONFIG.fsharpEntry), resolve(CONFIG.sassEntry)]
    } : {
        app: resolve(CONFIG.fsharpEntry),
        style: resolve(CONFIG.sassEntry)
    },
    ```
3. Next up, see the `module` field of the same `module.exports` object. You will see that it has a field named `rules`, which is a list of objects. Add the following object to this list:
    ```fsharp
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
            }
        ],
    },
    ```
Here's what your webpack config file should look like in the end: [webpack.config.js](https://gist.github.com/functionalprogrammer/df7f02e43d6c0f49b6ed75893b551f3a)

## I am Using the Minimal Template
1. Navigate to the `src/Client` folder and open the `webpack.config.js` file.
2. Find the `entry` field in the  `module.exports` object at the bottom of the file, and replace it with the following:
    ```javascript
    entry: {
        app: [
            resolve('./Client.fsproj'), 
            resolve('./style.sass'),
        ]
    }
    ```
3. Next up, see the `module` field of the same `module.exports` object. You will see that it has a field named `rules`, which is a list of objects. Add the following object to this list:
    ```javascript
    {
        test: /\.(sass|scss|css)$/,
        use: [
            'style-loader',
            'css-loader',
            { loader: 'sass-loader' }
        ],
    }
    ```
   
Here's what your webpack config file should look like in the end: [webpack.config.js](https://gist.github.com/functionalprogrammer/948f5c8f7293dd486c2bf2445c2d6b7c)

## There you have it!
You can now take advantage of Sass by writing to the `Style.scss` file.
