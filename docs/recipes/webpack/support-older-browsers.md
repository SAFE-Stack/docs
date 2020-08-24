# How Do I Add Support for Older Browsers?
## Polyfills
Simply put, a *polyfill* is a block of code that implements a feature on web browsers that do not support that feature. All we need to use polyfills where necessary is to tell [Babel](https://babeljs.io/) and [Fable](https://fable.io/) to do so in our webpack config file. You can find out more about polyfills [here](https://developer.mozilla.org/en-US/docs/Glossary/Polyfill#:~:text=A%20polyfill%20is%20a%20piece,do%20not%20natively%20support%20it.).

> Note that the standard template uses polyfills by default, and **this recipe only applies to the minimal template**.


---
#### 1. NPM Package
First, add [@babel/preset-env](https://www.npmjs.com/package/@babel/preset-env) NPM package to the Client.
> You can find out more about this package [here](https://babeljs.io/docs/en/babel-preset-env). 
>
> Also, see: [How Do I Add an NPM Package to the Client](../../add-npm-package-to-client.md).

#### 2. Configuration Object
Add the following object to the `webpack.config.js` file, which you can find in `src/Client`. 
The `useBuiltIns` option configures how `@babel/preset-env` handles polyfills. You can find out more about this option [here](https://babeljs.io/docs/en/babel-preset-env#usebuiltins).
```javascript
var babelOptions = {
    presets: [
        ['@babel/preset-env', {
            modules: false,
            useBuiltIns: 'usage',
            corejs: 3
        }]
    ],
}
```

#### 3. Fable Loader
Make the following modifications to the `fable-loader` object, which you can find at the bottom of `webpack.config` inside the `module` field of the `module.exports` object:
```javascript
{
    test: /\.fs(x|proj)?$/,
    use: { 
        loader: 'fable-loader', // <- Add a comma here.
        options: { babel: babelOptions } // <- Add this line.
    }
},
```

#### 4. Babel Loader
Make the following modifications to the `babel-loader` object, which you can find right below `fable-loader`.
```javascript
{
    test: /\.js$/,
    exclude: /node_modules/,
    use: { 
        loader: 'babel-loader', // <- Add a comma here.
        options: babelOptions // <- Add this line,
    }
},
```

---
Your `webpack.config.js` file should look like [this](https://gist.github.com/functionalprogrammer/deb9ed69e9c0040635cdca6f0ce35ae2) in the end.
 
## Done!
Babel will now use polyfills only where necessary to both enable your code to be run on older browsers and achieve optimal performance when they are not needed.
