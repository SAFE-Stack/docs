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

Open the generated folder.

Open the project in your IDE and rename the file `Program.fs` to `Build.fs`.

> If you just rename the file directly rather than in your IDE, then the Build project won't be able to find it unless you edit the Build.fsproj file as well

Open it and paste in the following code.

```fsharp
open Fake.Core
open Fake.IO

open Helpers

initializeContext()

let sharedPath = Path.getFullName "src/Shared"
let serverPath = Path.getFullName "src/Server"
let clientPath = Path.getFullName "src/Client"
let deployPath = Path.getFullName "deploy"

Target.create "Clean" (fun _ -> Shell.cleanDir deployPath)

Target.create "InstallClient" (fun _ -> run npm "install" ".")

Target.create "Run" (fun _ ->
    run dotnet "build" sharedPath
    [ "server", dotnet "watch run" serverPath
      "client", dotnet "fable watch --run webpack-dev-server" clientPath ]
    |> runParallel
)

open Fake.Core.TargetOperators

let dependencies = [

    "Clean"
        ==> "InstallClient"
        ==> "Run"
]

[<EntryPoint>]
let main args = runOrDefault args
```

#### 3. Helpers

Add another file to the `Build.fsproj` project called `Helpers.fs` and paste in the following code:

> Make sure you add the `Helpers.fs` file **above** `Build.fs`, as it is referenced by it.

```fsharp
module Helpers

open Fake.Core

let initializeContext () =
    let execContext = Context.FakeExecutionContext.Create false "build.fsx" [ ]
    Context.setExecutionContext (Context.RuntimeContext.Fake execContext)

module Proc =
    module Parallel =
        open System

        let locker = obj()

        let colors =
            [| ConsoleColor.Blue
               ConsoleColor.Yellow
               ConsoleColor.Magenta
               ConsoleColor.Cyan
               ConsoleColor.DarkBlue
               ConsoleColor.DarkYellow
               ConsoleColor.DarkMagenta
               ConsoleColor.DarkCyan |]

        let print color (colored: string) (line: string) =
            lock locker
                (fun () ->
                    let currentColor = Console.ForegroundColor
                    Console.ForegroundColor <- color
                    Console.Write colored
                    Console.ForegroundColor <- currentColor
                    Console.WriteLine line)

        let onStdout index name (line: string) =
            let color = colors.[index % colors.Length]
            if isNull line then
                print color $"{name}: --- END ---" ""
            else if String.isNotNullOrEmpty line then
                print color $"{name}: " line

        let onStderr name (line: string) =
            let color = ConsoleColor.Red
            if isNull line |> not then
                print color $"{name}: " line

        let redirect (index, (name, createProcess)) =
            createProcess
            |> CreateProcess.redirectOutputIfNotRedirected
            |> CreateProcess.withOutputEvents (onStdout index name) (onStderr name)

        let printStarting indexed =
            for (index, (name, c: CreateProcess<_>)) in indexed do
                let color = colors.[index % colors.Length]
                let wd =
                    c.WorkingDirectory
                    |> Option.defaultValue ""
                let exe = c.Command.Executable
                let args = c.Command.Arguments.ToStartInfo
                print color $"{name}: {wd}> {exe} {args}" ""

        let run cs =
            cs
            |> Seq.toArray
            |> Array.indexed
            |> fun x -> printStarting x; x
            |> Array.map redirect
            |> Array.Parallel.map Proc.run

let createProcess exe arg dir =
    CreateProcess.fromRawCommandLine exe arg
    |> CreateProcess.withWorkingDirectory dir
    |> CreateProcess.ensureExitCode

let dotnet = createProcess "dotnet"
let npm =
    let npmPath =
        match ProcessUtils.tryFindFileOnPath "npm" with
        | Some path -> path
        | None ->
            "npm was not found in path. Please install it and make sure it's available from your path. " +
            "See https://safe-stack.github.io/docs/quickstart/#install-pre-requisites for more info"
            |> failwith

    createProcess npmPath

let run proc arg dir =
    proc arg dir
    |> Proc.run
    |> ignore

let runParallel processes =
    processes
    |> Proc.Parallel.run
    |> ignore

let runOrDefault args =
    try
        match args with
        | [| target |] -> Target.runOrDefault target
        | _ -> Target.runOrDefault "Run"
        0
    with e ->
        printfn "%A" e
        1
```

#### 4. Move the files

You can now cut the `Build.fsproj`, `Build.fs` and `Helpers.fs` files and paste them in the root of your solution, then delete the Build folder as it is no longer needed.

> This is to allow us to execute `dotnet run` at the root of the solution rather than navigating into a `Build` subfolder.

#### 5. Add the project to the solution

Run the following command

```bash
dotnet sln add Build.fsproj
```
#### 6. Installing dependencies

You will need to install the following dependencies:

```
Fake.Core.Target
Fake.IO.FileSystem
```

We recommend migrating to [Paket](https://fsprojects.github.io/Paket/).
It is possible to use FAKE without Paket by creating an executable instead of a script file, however this will not be covered in this recipe.

#### 7. Run the app

At the root of the solution, `dotnet paket install` to install all your dependencies.

If you now execute `dotnet run`, the default target will be run. This will build the app in development mode and launch it locally.


To learn more about targets and FAKE in general, see [Getting Started with FAKE](https://fake.build/fake-gettingstarted.html#Minimal-example).

