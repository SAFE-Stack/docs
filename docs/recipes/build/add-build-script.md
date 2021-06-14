# How do I add build automation to the project?

## FAKE
[Fake](https://fake.build/) is a DSL for build tasks that is modular, extensible and easy to start with. Fake allows you to easily build, bundle, deploy your app and more by executing a single command.

> The standard template comes [with a FAKE project](../../../template-safe-commands) by default, so **this recipe only applies to the minimal template**.

---
#### 1. Create a build project

Create a new console app called 'Build' at the root of your solution

```fsharp
dotnet new console -lang f# -n Build
```

#### 2. Create a build script

In the generated folder open the `Program.fs` and paste in the following code.

```fsharp
open Fake.Core
open Fake.IO
open System

let redirect createProcess =
    createProcess
    |> CreateProcess.redirectOutputIfNotRedirected
    |> CreateProcess.withOutputEvents (Console.WriteLine) (Console.WriteLine)

let createProcess exe arg dir =
    CreateProcess.fromRawCommandLine exe arg
    |> CreateProcess.withWorkingDirectory dir
    |> CreateProcess.ensureExitCode

let dotnet = createProcess "dotnet"

let npm =
    let npmPath =
        match ProcessUtils.tryFindFileOnPath "npm" with
        | Some path -> path
        | None -> failwith "npm was not found in path."
    createProcess npmPath

let run proc arg dir =
    proc arg dir
    |> Proc.run
    |> ignore

let execContext = Context.FakeExecutionContext.Create false "build.fsx" [ ]
Context.setExecutionContext (Context.RuntimeContext.Fake execContext)

Target.create "Clean" (fun _ -> Shell.cleanDir (Path.getFullName "deploy"))

Target.create "InstallClient" (fun _ -> run npm "install" ".")

Target.create "Run" (fun _ ->
    run dotnet "build" (Path.getFullName "src/Shared")
    [ dotnet "watch run" (Path.getFullName "src/Server")
      dotnet "fable watch --run webpack-dev-server" (Path.getFullName "src/Client") ]
    |> Seq.toArray
    |> Array.map redirect
    |> Array.Parallel.map Proc.run
    |> ignore
)

open Fake.Core.TargetOperators

let dependencies = [
    "Clean"
        ==> "InstallClient"
        ==> "Run"
]

[<EntryPoint>]
let main args =
  try
      match args with
      | [| target |] -> Target.runOrDefault target
      | _ -> Target.runOrDefault "Run"
      0
  with e ->
      printfn "%A" e
      1
```

#### 3. Move the files

You can now cut the `Build.fsproj`, `Program.fs` files and paste them in the root of your solution, then delete the Build folder as it is no longer needed.

> This is to allow us to execute `dotnet run` at the root of the solution rather than navigating into a `Build` subfolder.

#### 4. Add the project to the solution

Run the following command

```bash
dotnet sln add Build.fsproj
```
#### 5. Installing dependencies

You will need to install the following dependencies:

```
Fake.Core.Target
Fake.IO.FileSystem
```

We recommend migrating to [Paket](https://fsprojects.github.io/Paket/).
It is possible to use FAKE without Paket by creating an executable instead of a script file, however this will not be covered in this recipe.

#### 6. Run the app

At the root of the solution, `dotnet paket install` to install all your dependencies.

If you now execute `dotnet run`, the default target will be run. This will build the app in development mode and launch it locally.


To learn more about targets and FAKE in general, see [Getting Started with FAKE](https://fake.build/fake-gettingstarted.html#Minimal-example).

