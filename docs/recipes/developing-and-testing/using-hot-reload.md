# How do I use hot reload?

Hot reload is a great time-saving technology and something that every developer will find useful.

Whenever changes are made to code, they are immediately reflected in the running application without needing to manually redeploy.

The specific way that this is achieved depends on the nature of the application.

---

In a SAFE app we have two distinct components, the Client and the Server.

Whether you are using the minimal or standard SAFE template, there is nothing more you need to do in order to get started with hot reload.

### Client reloading

If you deploy your application and then make a change in the Client, after a moment it will be reflected in the browser without a full re-deployment.

This achieved using the [hot module replacement](../../../feature-hmr) functionality provided by [webpack](https://webpack.js.org/).

### Server reloading

Server reloading isn't _quite_ as fully automated.

If you make a change in the Server code and save your work, the project _will_ automatically rebuild and deploy itself. Once this is complete however you will need to refresh your browser to see any visual changes.

> If you are using the minimal template, you need to make sure you launch the Server using `dotnet watch run` rather than just `dotnet run`. The standard template takes care of this step for you using its FAKE build script.