#How do I migrate to Yarn from NPM?

[Yarn](https://yarnpkg.com/) is an alternative Javascript package manager created by Facebook. 

When it was released it provided several features which were absent from NPM such as [offline caching](https://yarnpkg.com/features/offline-cache) and deterministic dependency resolution. 

Today however, NPM has largely gained feature parity with Yarn, although they still do things slightly differently and therefore many people prefer one over the other.

The SAFE template uses NPM out of the box, but it is easy to migrate to Yarn if you wish.

## Switching to Yarn

1.) You will need to [install Yarn](https://classic.yarnpkg.com/en/docs/install#windows-stable) on your machine. 

> Please note: Version 2 of Yarn is [not currently supported](https://github.com/SAFE-Stack/SAFE-template/issues/329).

2.) Navigate to the **Client** directory and simply run the command:
```powershell
yarn
```
This will read your existing NPM package.json file and create a [yarn.lock](https://classic.yarnpkg.com/en/docs/yarn-lock) file in the project directory.

> If you are using Visual Studio you will need to include the lock file in your project if you wish to see it in the Solution explorer.

3.) If you want to make sure that Yarn is using the same package versions that NPM had in its lock file, run

```powershell
yarn import
```

Once this process is complete you can remove the NPM package-lock.json as it is no longer needed.

For more details on migration see the [official docs](https://classic.yarnpkg.com/en/docs/migrating-from-npm/).

## Running the application

Now that you have switched to using Yarn, you will need to modify the way in which the client application is built and launched.

The way in which you do this depends on whether you have started with the minimal or full template, as the latter uses FAKE to manage the build process.

### Launching the application from the minimal template

1.) Navigate to the **Client** project directory

2.) Run the command 
```powershell
yarn webpack-dev-server
```

3.) Navigate to the **Server** project directory and run
```powershell
dotnet run
```

### Launching the application from the minimal template

1.) Navigate to the **Client** project directory

2.) Run the command 
```powershell
yarn webpack -p
```

3.) Navigate to the **Server** project directory and run
```powershell
dotnet run
```

### Launching the application from the full template

1.) Open the build.fsx file that is in the root of the solution directory.

2.) Replace 
```fsharp
let npm args workingDir =
    let npmPath =
        match ProcessUtils.tryFindFileOnPath "npm" with
        | Some path -> path
        | None ->
            "npm was not found in path. Please install it and make sure it's available from your path. " +
            "See https://safe-stack.github.io/docs/quickstart/#install-pre-requisites for more info"
            |> failwith

    let arguments = args |> String.split ' ' |> Arguments.OfArgs

    Command.RawCommand (npmPath, arguments)
    |> CreateProcess.fromCommand
    |> CreateProcess.withWorkingDirectory workingDir
    |> CreateProcess.ensureExitCode
    |> Proc.run
    |> ignore
```
with
```fsharp
let yarn args workingDir =
    let yarnPath =
        match ProcessUtils.tryFindFileOnPath "yarn" with
        | Some path -> path
        | None ->
            "yarn was not found in path. Please install it and make sure it's available from your path. " +
            "See https://safe-stack.github.io/docs/quickstart/#install-pre-requisites for more info"
            |> failwith

        let arguments = args |> String.split ' ' |> Arguments.OfArgs

        Command.RawCommand (yarnPath, arguments)
        |> CreateProcess.fromCommand
        |> CreateProcess.withWorkingDirectory workingDir
        |> CreateProcess.ensureExitCode
        |> Proc.run
        |> ignore
```

3.) Replace
```fsharp
Target.create "InstallClient" (fun _ -> npm "install" clientPath)
```
with
```fsharp
Target.create "InstallClient" (fun _ -> yarn "install --frozen-lockfile" clientPath)
```

3.) Replace
```fsharp
Target.create "Bundle" (fun _ ->
    dotnet (sprintf "publish -c Release -o \"%s\"" deployDir) serverPath
    npm "run build" clientPath
)
```
with
```fsharp
Target.create "Bundle" (fun _ ->
    dotnet (sprintf "publish -c Release -o \"%s\"" deployDir) serverPath
    yarn "run build" clientPath
)
```

4.) Replace
```fsharp
Target.create "Run" (fun _ ->
    dotnet "build" sharedPath
    [ async { dotnet "watch run" serverPath }
      async { npm "run start" clientPath } ]
    |> Async.Parallel
    |> Async.RunSynchronously
    |> ignore
)
```
with
```fsharp
Target.create "Run" (fun _ ->
    dotnet "build" sharedPath
    [ async { dotnet "watch run" serverPath }
      async { yarn "run start" clientPath } ]
    |> Async.Parallel
    |> Async.RunSynchronously
    |> ignore
)
```

4.) Finally, replace
```fsharp
Target.create "RunTests" (fun _ ->
    dotnet "build" sharedTestsPath
    [ async { dotnet "watch run" serverTestsPath }
      async { npm "run test:live" clientPath } ]
    |> Async.Parallel
    |> Async.RunSynchronously
    |> ignore
)
```
with
```fsharp
Target.create "RunTests" (fun _ ->
    dotnet "build" sharedTestsPath
    [ async { dotnet "watch run" serverTestsPath }
      async { yarn "run test:live" clientPath } ]
    |> Async.Parallel
    |> Async.RunSynchronously
    |> ignore
)
```