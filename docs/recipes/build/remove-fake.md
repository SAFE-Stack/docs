# How do I remove the use of FAKE?
The standard SAFE template comes with [FAKE](https://fake.build/), and a build script at the root of the solution. This means that running each of the following tasks is as easy as running a single command inside a terminal at the root level:
1. Running the application
2. Bundling the application
3. Deploying the application to Azure

It is important to note that having removed FAKE, you will have to follow a more manual approach to each of these processes. This recipe will include instructions on how to build and deploy the application after removing FAKE.

> Note that the minimal template does not have FAKE installed by default, and **this recipe only applies to the standard template**.

#### 1. Build Script
Find `build.fsx` at the root of the solution and delete it.
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
```fsharp
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
Bundling the application now also becomes a two step process.
#### 1. Bundle the Client
Execute `npm run build` inside a terminal *at the root of the solution*.
#### 2. Bundle the Server
 Navigate to `src/Server` inside a terminal and execute the following command:
```bash
dotnet publish -c release -o ..\..\deploy\
```

---

You can now find the outcome of the bundling process in the `deploy` folder at the root of the solution.
