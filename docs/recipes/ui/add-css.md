# How Do I Use CSS with SAFE?

## Adding the Stylesheet
Then, you will need to create an CSS file in the `src/Client` folder of your solution e.g `style.css`. You might also want the stylesheet to be “included” in the `Client.fsproj` file in order for it to be seen in the *solution view* of your IDE. This is not essential, but recommended.

- - - -
> The following steps will vary depending on whether you’re using the standard template or the minimal one. Follow the steps that corresponds to the version of the SAFE template you’re using and ignore the rest.  
- - - -

## I am Using the Standard Template
### 1. Webpack Config
Navigate to the `src/Client` folder and open the `webpack.config.js` file.

### 2. cssEntry
Find the `cssEntry` variable inside the `CONFIG` module and replace it with the following:
```javascript
cssEntry: './style.css',
```

### 3. Entry
 Find the `entry` field in the  `module.exports` object at the bottom of the file, and replace it with the following:
```javascript
entry: isProduction ? {
    app: [resolve(CONFIG.fsharpEntry), resolve(CONFIG.cssEntry)]
} : {
    app: resolve(CONFIG.fsharpEntry),
    style: resolve(CONFIG.cssEntry)
},
```

Here’s what your webpack config file should look like in the end:  [webpack.config.js](https://gist.github.com/functionalprogrammer/d089ea05760a92174880fb2dc0f9650e) 

## I am Using the Minimal Template
### 1. Webpack Config
Navigate to the `src/Client` folder and open the `webpack.config.js` file.

### 2. Entry
Find the `entry` field in the  `module.exports` object at the bottom of the file, and replace it with the following:
```javascript
entry: { 
    app: [
        resolve('./Client.fsproj'), 
        resolve('./style.css')
    ]
},
```

Here’s what your webpack config file should look like in the end:  [webpack.config.js](https://gist.github.com/functionalprogrammer/d24267f199741453005c800cd0e4df1d) 

## There you have it!
You can now style your app by writing to the `style.css` file.