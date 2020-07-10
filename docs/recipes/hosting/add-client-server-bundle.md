# How do I add a client / server bundle?

When developing your SAFE application, the local runtime experience uses WebPack to run client and redirects API calls to the server on a [different port](/faq-build). However, when you *deploy* your application, you'll need to run your Saturn server which will serve up statically-built client resources (HTML, JavaScript, CSS etc.).

## 1. Creating a bundle using the Minimal
If you created your SAFE app using the **minimal** option, you can create a bundle using the following command:

### Building the Client application
Bundle the client (Fable) application.

```bash
cd src\client
npm run build
```

This will build the client project and copy all outputs into `/deploy/public`.

> The `run build` command run `webpack` using the Production config, and the `webpack.config.js` file specifies its `output` as `/deploy/public`.

### Building the Server application
Bundle the server (Saturn) application.

```bash
cd src\server
dotnet publish -c release -o ..\..\deploy\
```

This will bundle the server project and copy all outputs into the `/deploy/` folder.

## 2. Creating a bundle using the Full template
If you created your SAFE app using the **minimal** option, you can create a bundle using the following command:

```cmd
dotnet fake build
```

This will build and package up both the client and server and place them into the `/deploy` folder at the root of the repository.

## 3. Test the bundle
1. Navigate to the `deploy` folder at the root of your repository.
2. Run the `server.exe` application.
3. Navigate in your browser to `http://localhost:8085`.

You should now see your SAFE application.