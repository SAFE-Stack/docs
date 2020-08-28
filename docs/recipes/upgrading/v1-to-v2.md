# How do I upgrade from SAFE v1 to v2?

There have been a number of changes between the first and second major versions of the SAFE template.

However, it isn't to difficult to update an existing v1 style project to the v2 structure if you wish to do so.

> If you haven't done so already then you will need to install the prequisites listed in the [Quick Start](http://localhost:8000/quickstart/) guide.

#### 1. Install the v2 template

Download and install the Safe V2 template by running the following command:

```powershell
dotnet new -i SAFE.Template
```

#### 2. Create a v2 project

Create a new project from the v2 template. We will use this as a basis for our conversion.

Run the command
```fsharp
dotnet new SAFE
```
to create a new project.

#### 3. Update dotnet tools

Open the v1 project's root directory. You will find a folder called `.config` which contains a file named `dotnet-tools.json`. This will list any dotnet tooling required to run the project.

Copy the contents of the equivalent file in the v2 template and overwrite the v1 file contents with them.

>  **Important** If you have installed dotnet tools in addition to those that come with the v1 template, you will need to make sure to continue to include them when you do this replacement.

#### 4. Replace Yarn with NPM

The v2 template no longer uses Yarn to manage javascript dependencies. It is easy to switch over to NPM. 

- Delete the `yarn.lock` file at the root of the v1 solution.

- If there is a `package-lock.json` file, delete that too.

- Delete the contents of the `node_modules` folder.

- Open the `package.json` file.

- Replace its contents with the equivalent file from the v2 template.

> **Important** If you have installed javascript packages in addition to those that come with the v1 template, you will need to make sure to continue to include them when you do this replacement.

- Run the command
```powershell
npm install
```
to install the packages and generate a package-lock.json file.

#### 5. Update Paket dependencies

- Open the `paket.dependencies` file in the root of the solution.

- Replace its contents with the equivalent file from the v2 template.

>  **Important** If you have installed nuget packages in addition to those that come with the v1 template, you will need to make sure to continue to include them when you do this replacement. There are no longer groups for the Client and Server dependency graphs. Simply include them at the root level.

- Delete the paket.lock file in the same directory.

- Open the `paket.references` files in the Server and Client projects (And any others you may have created) and remove the `Client` and `Server` groups from them, leaving package names simply listed at the root level.

- Run 
```powershell
dotnet paket install
```
to install all dependencies and generate a new lock file.



#### 6. Update FAKE build script

- Open the build.fsx file at the root of the v1 solution.

- Assuming you have not made any modifications to the build script that came with the v1 template, you can just replace the entire contents with that of the equivalent file in the v2 template.

> The v2 FAKE script includes targets which build its Client and Server Test projects. If you are not planning on importing the Test projects to the v1 project you are upgrading then you can remove these targets.

- If you have used [Farmer](https://compositionalit.github.io/farmer/) to configure Azure in your v1 project, notice that there is an `Azure` target in the v2 build script which executes a basic Farmer deployment. You can replace this with your existing Farmer script. Otherwise, you will need to replace the name of the v2 project file with the name of your v1 project here, so that it deploys with the correct url and resource names. Once you have completed this recipe, you will be able to run the deployment using
```powershell
dotnet fake build -t Azure
```
> Make sure you do not include underscores in the Azure target name field, as this will cause the deployment to fail.


#### 7. Update webpack config

- Open the `webpack.config.js` file at the root of the solution.
- Again, assuming you have not made any modifications to the build script that came with the v1 template, you can just replace the entire contents with that of the equivalent file in the v2 template.

- If you wish to a CSS file, you will need to add it to the webpack file. Add the path to the `CONFIG` object like this
```javascript
cssEntry: './src/Client/style.scss',
```
where the path matches the location of your stylesheet.

- In the `module.exports` object, replace
```javascript
entry: {
        app: resolve(CONFIG.fsharpEntry)
},
```
with 
```javascript
entry: isProduction ? {
        app: [resolve(CONFIG.fsharpEntry), resolve(CONFIG.cssEntry)]
} : {
        app: resolve(CONFIG.fsharpEntry),
        style: resolve(CONFIG.cssEntry)
    },
```

#### 8. Switched to a project for Shared files

The v1 template used shared files to allow code reuse between the Server and Client.

The v2 template now has a dedicated project for shared content.

- Add a new **.Net Standard** project to the solution by running
```powershell
cd src
dotnet new ClassLib -lang F# -o Shared
cd ..
dotnet sln add src/Shared
```

- Remove the 'Library.fs' module from the project and include any shared files you have been using (such as **Shared.fs**).

- Reference the Shared project from the Server and Client projects (and any others which need to access shared content) by running the commands

```powershell
cd src
dotnet add Client reference Shared
dotnet add Server reference Shared
```

etc.

#### 9. Update Saturn application

- Open the `Server.fs` module at the root of the **Server** project (or whatever module contains your Saturn `application` expression).

- Delete the following content:
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
- In the Saturn `application` expression, replace
```fsharp
url ("http://0.0.0.0:" + port.ToString() + "/")
```
with
```fsharp
url "http://0.0.0.0:8085"
```
and
```fsharp
use_static publicPath
```
with
```fsharp
use_static "public"
```

#### 10. Check that it runs

That should be everything you need to do.

Run 
```powershell
dotnet fake build -t run
```
at the root of the solution to launch the app and check everything is working as expected.

If you have problems loading your website, carefully check that you haven't missed out any javascript or nuget packages when overwriting the paket and package files. The console output will usually give you a good guide if this is the case.




