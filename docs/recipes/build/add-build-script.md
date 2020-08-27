# How do I add a build script to the project?
> The standard template comes with FAKE installed by default, and **this recipe only applies to the minimal template**.

## FAKE
[Fake](https://fake.build/) is a DSL for build tasks that is modular, extensible and easy to start with. Having installed `fake-cli` and adding a build script to yoru project, you can easily build, bundle, deploy your app and more, by executing a single command.

## Paket
Before moving on, we recommend migrating to [Paket](https://fsprojects.github.io/Paket/). In fact, **this is a prerequisite for this recipe**. It is possible to use FAKE without Paket by creating an executable instead of a script file, however this will not be covered in this recipe.

If you’re *not* using Paket to handle your dependencies, go through the [Migrate to Paket](../../package-management/migrate-to-paket.md) recipe before continuing.

---
#### 1. The Build Dependencies
Paste the following at the end of your `paket.dependencies` file, which is at the root of the solution.
```
group Build
    source https://api.nuget.org/v3/index.json
    framework: netstandard2.0
    storage: none

    nuget FSharp.Core
    nuget Fake.Core.Target
    nuget Fake.DotNet.Cli
    nuget Fake.IO.FileSystem
```
Then, execute `paket install` to install these dependencies.

#### 2. Fake CLI Tool
Run the following commands at the root folder of your solution to install FAKE as a local tool:
```bash
dotnet tool install fake-cli
dotnet tool restore
```

#### 3. The Build Script
Add an F# script file to the root folder of your solution named `build.fsx` and paste the following code into it. 

At a high level, we have three targets that run in a sequence:
1. `Clean` removes any content from the `./deploy` directory.
2. `InstallClient` installs the NPM packages that the client needs.
3. `Bundle` runs `dotnet publish` at the server directory and `npm build` at the client directory, combining the output of each into the `./deploy` folder.

To learn more about targets and FAKE in general, see [Getting Started with FAKE](https://fake.build/fake-gettingstarted.html#Minimal-example).
```fsharp
#r "paket: groupref build //"
#load "./.fake/build.fsx/intellisense.fsx"
#r "netstandard"

open Fake.Core
open Fake.Core.TargetOperators
open Fake.DotNet
open Fake.IO

Target.initEnvironment ()

let serverPath = Path.getFullName "./src/Server"
let clientPath = Path.getFullName "./src/Client"
let deployDir = Path.getFullName "./deploy"

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

let dotnet cmd workingDir =
    let result = DotNet.exec (DotNet.Options.withWorkingDirectory workingDir) cmd ""
    if result.ExitCode <> 0 then failwithf "'dotnet %s' failed in %s" cmd workingDir

Target.create "Clean" (fun _ -> Shell.cleanDir deployDir)

Target.create "InstallClient" (fun _ -> npm "install" clientPath)

Target.create "Bundle" (fun _ ->
    dotnet (sprintf "publish -c Release -o \"%s\"" deployDir) serverPath
    npm "run build" clientPath
)

"Clean"
    ==> "InstallClient"
    ==> "Bundle"

Target.runOrDefaultWithArguments "Bundle"
```

#### 4. Build the Application
Now, you can easily build your application by running the following command at the root directory of your solution:
```bash
dotnet fake build
```