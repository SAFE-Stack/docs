# How do I add a client / server bundle?

When developing your SAFE application, the local runtime experience uses WebPack to run client and redirects API calls to the server on a [different port](/faq-build). However, when you *deploy* your application, you'll need to run your Saturn server which will serve up statically-built client resources (HTML, JavaScript, CSS etc.).

## Creating a bundle using NPM (Minimal)
If you created your SAFE app using the **minimal** option, you can create a bundle using the following commands:

### 1. Building the Client application
Bundle the client (Fable) application.

```bat
cd src\client
npm run build
```

This will build the client project and copy all outputs into `/deploy/public` e.g.

```cmd
Name
----
app.js
favicon.png
index.html
vendors~app.js
```

> The `run build` command run `webpack` using the Production config, and the `webpack.config.js` file sets the `output` to the `/deploy/public`.

### 2. Building the Server application


## Creating a bundle using FAKE (Full)
If you created your SAFE app using the **minimal** option, you can create a bundle using the following command:

