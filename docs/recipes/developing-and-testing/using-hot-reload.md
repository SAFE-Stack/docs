# How do I use hot reload?

Hot reload is a great time-saving technology and something that every developer will find useful. Whenever changes are made to code, they are immediately reflected in the running application without needing to manually redeploy. The specific way that this is achieved depends on the nature of the application.

---

In a SAFE app we have two distinct components, the Client and the Server. Whether you are using the minimal or standard SAFE template, there is nothing more you need to do in order to get started with hot reload.

## Client reloading
If you deploy your application and then make a change in the Client, after a moment it will be reflected in the browser without a full re-deployment. Importantly, the state of your application will be retained across the deployment, so you can continue where you left off.
This achieved using the [hot module replacement](https://webpack.js.org/guides/hot-module-replacement/) functionality provided by [webpack](https://webpack.js.org/).

### To Add Hot Module Replacement manually
If your client project has been hand-rolled, or you simply wish to see how to add it from scratch:

#### 1. Configure the Webpack Dev Server
Add the following to the `devServer` object of your webpack config:

```javascript
var devServer = {
    // other fields elided...
    hot: true,
    proxy : {
        // Redirect websocket requests that start with /socket/ to the server on the port 8085
        // This is used by Hot Module Replacement
        '/socket/**': {
            target: 'http://localhost:8085',
            ws: true
        }
    }
}
```

#### 2. Configure webpack module exports
Import and create an instance `HotModuleReplacementPlugin` at the top of the webpack configuration file, and ensure that the plugin is added to `module.exports`:

```javascript
// Import and create the HMR plugin
var { HotModuleReplacementPlugin } = require('webpack');
var hmrPlugin = new HotModuleReplacementPlugin();

// other configuration...

module.exports = {
    // Add the HMR plugin to the module
    plugins : [ /* other plugins... */, hmrPlugin ]
}
```

#### 3. Update your F# client app
First, add the Fable.Elmish.Hmr package to the client:

```powershell
dotnet add package Fable.Elmish.Hmr
```

Then, open the Elmish.HMR namespace in your app:

```fsharp
#if DEBUG
open Elmish.Debug
open Elmish.HMR
#endif
```

> Best practice is to include hot module reloading in debug (not production) mode only, since production applications will not benefit from HMR and will only result in an increased bundle size.

## Server reloading
Server reloading isn't _quite_ as fully automated.

If you make a change in the Server code and save your work, the project _will_ automatically rebuild and launch itself. Once this is complete however you will need to refresh your browser to see any visual changes.

> If you are using the minimal template, you need to make sure you launch the Server using `dotnet watch run` rather than just `dotnet run`. The standard template takes care of this step for you using its FAKE build script. If you have already restored your NuGet dependencies, you can get a little boost in restart speed by using `dotnet watch run --no-restore` as well.