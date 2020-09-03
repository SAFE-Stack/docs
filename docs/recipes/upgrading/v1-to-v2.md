# How do I upgrade from SAFE v1 to v2?

There have been a number of changes between the first and second major versions of the SAFE template. This guide shows you how to upgrade your v1 project to v2.

> If you haven't done so already then you will need to install the prequisites listed in the [Quick Start](http://localhost:8000/quickstart/) guide.

### Terminology for this Recipe:

* ***"Overwrite"***: Take the file from the "new" V2 template and copy it over the equivalent file in your existing project.
* ***"Delete"***: Delete the file from your existing project. It is no longer required.
* ***"Add"***: Add the file to your existing project. It is a new file added in V2.

#### 1. Install the v2 template
Download and install the latest SAFE Stack V2 template by running the following command:

```bash
dotnet new -i SAFE.Template
```

#### 2. Create a v2 project
Create a new SAFE project in the `safetemp` folder. We will use this as a basis for our conversion.

```fsharp
dotnet new SAFE -o safetemp
```

#### 3. Branch your code
We advise committing or stashing any unsaved changes to your code and making a new branch to perform the upgrade.

You can then test it in isolation and then safely merge back in.

#### 4. Update dotnet tools
This file lists any custom dotnet tools used.

* **Overwrite** the `.config/dotnet-tools.json` file.

> **Important**! If you have installed extra dotnet tools,you will need to add them back in manually.

#### 4. Replace Yarn with NPM
The v2 template no longer uses Yarn to manage JavaScript dependencies, and instead uses NPM.

* **Delete** the `yarn.lock` file.
* **Overwrite** the `package.json` file.
* **Overwrite** the `package-lock.json` file.

> **Important**! If you have installed extra NPM packages, you will need to add them back in manually.

If you *have* added any extra NPM packages, run this command to generate a new `package-lock.json`.
```bash
npm install
```

#### 5. Update Paket dependencies
* **Overwrite** the `paket.dependencies` file in the root of the solution.
* **Overwrite** the `paket.lock` file.
* **Overwrite** all `paket.references` files.

**Important** If you have installed extra NuGet packages, you will need to add them back in manually to the dependencies and references files.

> There are no longer separate groups for the Client and Server dependency graphs. Simply include any custom packages in the main group.

Run paket to update project references:

```bash
dotnet paket install
```

* If you *have* added any extra NuGet packages, this command will also generate a new `paket.lock` file.

#### 6. Update FAKE build script
* **Overwrite** the `build.fsx` FAKE script.

**Important** If you have made any modifications to the build script e.g. extra targets, you will need to add them back in manually.

> The v2 FAKE script includes targets which build its Client and Server Test projects. If you are not planning on importing the Test projects to the v1 project you are upgrading then you can remove these targets.

> The v2 FAKE script includes a target for Azure deployment using Farmer. If you are not using Azure deployments, you can safely delete this target.

#### 7. Update webpack config
* **Overwrite** the `webpack.config.js` file.

**Important** If you have made any modifications to the webpack file, you will need to apply them back in manually.

* If you were using a CSS files, make sure to follow the [Stylesheet recipe](recipes/ui/add-style.md) to add them back in.

#### 8. Switch to a project for Shared files
The v1 template used shared files to allow code reuse between the Server and Client. The v2 template now has a dedicated project for shared content.

* Add a new **.Net Standard** project to the solution by running
```bash
cd src
dotnet new ClassLib -lang F# -o Shared
cd ..
dotnet sln add src/Shared
```

* Remove the `Library.fs` module from the project and include any shared files you have been using (such as **Shared.fs**).

* Reference the Shared project from the Server and Client projects (and any others which need to access shared content):

```bash
cd src
dotnet add Client reference Shared
dotnet add Server reference Shared
```

#### 9. Update Saturn application

* Open the `Server.fs` module at the root of the **Server** project (or whatever module contains your Saturn `application` expression).

* Delete the following content:
```fsharp
let tryGetEnv key =
    match Environment.GetEnvironmentVariable key with
    | x when String.IsNullOrWhiteSpace x -> None
    | x -> Some x

let publicPath = Path.GetFullPath "../Client/public"

let port =
    "SERVER_PORT"
    |> tryGetEnv |> Option.map uint16 |> Option.defaultValue 8085us
```
* In the Saturn `application` expression, overwrite
```fsharp
url ("http://0.0.0.0:" + port.ToString() + "/")
use_static publicPath
```
with
```fsharp
url "http://0.0.0.0:8085"
use_static "public"
```

#### 10. Check that it runs

Run
```bash
dotnet fake build -t run
```
at the root of the solution to launch the app and check everything is working as expected.

If you have problems loading your website, carefully check that you haven't missed out any javascript or nuget packages when overwriting the paket and package files. The console output will usually give you a good guide if this is the case.




