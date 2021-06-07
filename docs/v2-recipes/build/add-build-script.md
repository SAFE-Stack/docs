# How do I add a build script to the project?

## FAKE
[Fake](https://fake.build/) is a DSL for build tasks that is modular, extensible and easy to start with. Fake allows you to easily build, bundle, deploy your app and more by executing a single command.

> The standard template comes [with a FAKE script](../../../template-fake) by default, and **this recipe only applies to the minimal template**.

## Paket
Before moving on, we recommend migrating to [Paket](https://fsprojects.github.io/Paket/). In fact, **this is a prerequisite for this recipe**. It is possible to use FAKE without Paket by creating an executable instead of a script file, however this will not be covered in this recipe.

If you’re *not* using Paket to handle your dependencies, go through the [Migrate to Paket](../../package-management/migrate-to-paket) recipe before continuing.

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
The script builds and publishes the client and server applications in their production / release modes, and copies the outputs of both into a `deploy` folder at the root of the repository.

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
    let npmPath = ProcessUtils.tryFindFileOnPath "npm" |> Option.get
    let arguments = args |> String.split ' ' |> Arguments.OfArgs

    Command.RawCommand (npmPath, arguments)
    |> CreateProcess.fromCommand
    |> CreateProcess.withWorkingDirectory workingDir
    |> CreateProcess.ensureExitCode
    |> Proc.run
    |> ignore

let dotnet cmd workingDir = DotNet.exec (DotNet.Options.withWorkingDirectory workingDir) cmd ""

Target.create "Bundle" (fun _ ->
    Shell.cleanDir deployDir
    npm "install" clientPath
    dotnet (sprintf "publish -c Release -o \"%s\"" deployDir) serverPath
    npm "run build" clientPath
)

"Clean"
    ==> "InstallClient"
    ==> "Bundle"

Target.runOrDefaultWithArguments "Bundle"
```

#### 4. Build the Application
You can now build your application by running the following command at the root directory of your solution:
```powershell
dotnet fake build
```