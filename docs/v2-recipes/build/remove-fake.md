# How do I remove the use of FAKE?
[FAKE](https://fake.build/) is a tool for build automation. The standard SAFE template comes with a [ready-made build script](/template-fake) at the root of the solution that provides support for many common SAFE tasks.

If you would prefer not to use FAKE, you can of course simply ignore it, but this recipes shows how to completely remove it from your repository. It is important to note that having removed FAKE, you will have to follow a more manual approach to each of these processes. This recipe will only include instructions on how to build and deploy the application after removing FAKE.

> Note that the minimal template does not have FAKE installed by default, and **this recipe only applies to the standard template**.

#### 1. Build Script
Delete `build.fsx` at the root of the solution.
#### 2. Dependencies
Find the following block of code inside the `paket.dependencies` file that’s also at the root of the solution and delete it.
```fsharp
group Build
    source https://api.nuget.org/v3/index.json
    framework: netstandard2.0
    storage: none

    nuget FSharp.Core
    nuget Fake.Core.ReleaseNotes
    nuget Fake.Core.Target
    nuget Fake.DotNet.Cli
    nuget Fake.IO.FileSystem
    nuget Farmer
```
#### 3. Paket Install
Then, execute `paket install` in your terminal at the root of the solution. This will remove the dependencies whose names were included in the Build group.
#### 4. Fake Tool
The final step is to delete the FAKE tool. Execute the following command at the root of the solution to do this:
```powershell
dotnet tool uninstall fake-cli
```

## Running the App
Now that you have FAKE removed, you will have to separately run the server and the client.
#### 1. Start the Client
Execute `npm start` inside a terminal *at the root of the solution*.
#### 2. Start the Server
Navigate to `src/Server` inside a terminal and execute `dotnet run`.

---

The app will now be running at `http://0.0.0.0:8080/`. Navigate to this address in a browser to see your app running.

## Bundling the App
See [this guide](../build/bundle-app.md#2-im-using-the-minimal-template) to learn how to package a SAFE application for deployment to e.g. Azure.

---

You can now find the outcome of the bundling process in the `deploy` folder at the root of the solution.
