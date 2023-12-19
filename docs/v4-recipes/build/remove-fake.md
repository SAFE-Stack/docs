# How do I remove the use of FAKE?
[FAKE](https://fake.build/) is a tool for build automation. The standard SAFE template comes with a [ready-made build project](../../template-safe-commands.md) at the root of the solution that provides support for many common SAFE tasks.

If you would prefer not to use FAKE, you can of course simply ignore it, but this recipes shows how to completely remove it from your repository. It is important to note that having removed FAKE, you will have to follow a more manual approach to each of these processes. This recipe will only include instructions on how to build and deploy the application after removing FAKE.

> Note that the minimal template does not use FAKE by default, and **this recipe only applies to the standard template**.

#### 1. Build project
Delete `Build.fs`, `Build.fsproj`, `Helpers.fs`, `paket.references` at the root of the solution.

#### 2. Dependencies
Remove the following dependencies 
```fsharp
dotnet paket remove Fake.Core.Target
dotnet paket remove Fake.IO.FileSystem
dotnet paket remove Farmer
```

## Running the App
Now that you have the FAKE dependencies removed, you will have to separately run the server and the client.

#### 1. Start the Server
Navigate to `src/Server` inside a terminal and execute `dotnet run`.

#### 2. Start the Client

Execute the following commands inside a terminal *at the root of the solution*.

```bash
dotnet tool restore
npm install
dotnet fable src/Client --run webpack-dev-server
``` 

---

The app will now be running at `http://0.0.0.0:8080/`. Navigate to this address in a browser to see your app running.

## Bundling the App
See [this guide](../build/bundle-app.md#2-im-using-the-minimal-template) to learn how to package a SAFE application for deployment to e.g. Azure.

---