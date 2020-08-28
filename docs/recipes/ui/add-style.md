# How Do I Use stylesheets with SAFE?
If you wish to use your own CSS or SASS stylesheets with SAFE apps, you can embed either them through webpack. The template already includes all required NPM packages you may need, so you will only need to configure webpack to reference your stylesheet and include in the outputs.

## Adding the Stylesheet
First, create a CSS file in the `src/Client` folder of your solution e.g `style.css`.

> The same approach can be taken for `.scss` files.

## Configuring WebPack

### I'm using the Standard Template
#### 1. Link to the stylesheet

Inside the `webpack.config.js` file, add the following variable to the `CONFIG` object, which points to the style file you created previously.
```javascript
cssEntry: './src/Client/style.css',
```

#### 2. Embed CSS into outputs
 Find the `entry` field in the  `module.exports` object at the bottom of the file, and replace it with the following:
```javascript
entry: isProduction ? {
    app: [resolve(CONFIG.fsharpEntry), resolve(CONFIG.cssEntry)]
} : {
    app: resolve(CONFIG.fsharpEntry),
    style: resolve(CONFIG.cssEntry)
},
```

This combines the css and F# outputs into a single bundle for production, and separately for dev.

### I'm using the Minimal Template
#### 1. Embed CSS into outputs
Find the `entry` field in the  `module.exports` object at the bottom of the file, and replace it with the following:
```javascript
entry: {
    app: [
        resolve('./src/Client/Client.fsproj'),
        resolve('./src/Client/style.css')
    ]
},
```

## There you have it!
You can now style your app by writing to the `style.css` file.