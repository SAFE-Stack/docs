# How do I bundle my SAFE application?

When developing your SAFE application, the local runtime experience uses Vite to run the client and redirect API calls to the server on a [different port](../../../faq-build). However, when you *deploy* your application, you'll need to run your Saturn server which will serve up statically-built client resources (HTML, JavaScript, CSS etc.).

#### 1. Run the FAKE script
If you created your SAFE app using the recommended defaults, your application already has a FAKE script which will do the bundling for you. You can create a bundle using the following command:

```cmd
dotnet run Bundle
```

This will build and package up both the client and server and place them into the `/deploy` folder at the root of the repository.

> See [here](../../../template-safe-commands) for more details on this build target.

## Testing the bundle
1. Navigate to the `deploy` folder at the root of your repository.
2. Run the `Server.exe` application.
3. Navigate in your browser to `http://localhost:5000`.

You should now see your SAFE application.

## Further reading
See [this article](/docs/faq-build) for more information on architectural concerns regarding the move from dev to production and bundling SAFE Stack applications.
