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

> If you have installed dotnet tools in addition to those that come with the v1 template, you will need to make sure to continue to include them when you do this replacement.

#### 4. Replace Yarn with NPM

The v2 template no longer uses Yarn to manage javascript dependencies. It is easy to switch over to NPM. 

- Delete the `yarn.lock` file at the root of the v1 solution.
- Open the `package.json` file in the same directory.
- Replace its contents with the equivalent file from the v2 template.

> If you have installed javascript packages in addition to those that come with the v1 template, you will need to make sure to continue to include them when you do this replacement.

- Run the command
```powershell
npm install
```
to install the packages and generate a package.lock file.

#### 5. Update Paket dependencies

- Open the `paket.dependencies` file in the root of the solution.
- Replace its contents with the equivalent file from the v2 template.

> If you have installed nuget packages in addition to those that come with the v1 template, you will need to make sure to continue to include them when you do this replacement. There are no longer groups for the Client and Server dependency graphs. Simply include them at the root level.

- Delete the paket.lock file in the same directory.
- Run 
```fsharp
dotnet paket install
```
to install all dependencies and generate a new lock file.

- Open the `paket.references` files in the Server and Client projects (And any others you may have created) and remove the `Client` and `Server` groups from them, leaving package names simply listed at the root level.

#### 6. Update the FAKE build script

- Open the build.fsx file at the root of the v1 solution.

- Assuming you have not made any modifications to the build script that came with the v1 template, you can just replace the entire contents with that of the equivalent file in the v2 template.

> The v2 FAKE script includes targets which build its Client and Server Test projects. If you are not planning on importing the Test projects to the v1 project you are upgrading then you can remove these targets.

