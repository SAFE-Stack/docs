# How do I upgrade from SAFE v3 to v4?

This guide shows you how to upgrade your v3 project to v4.

> If you haven't done so already then you will need to install the prequisites listed in the [Quick Start](../../../quickstart) guide.

### Terminology for this Recipe:

* ***"Overwrite"***: Take the file from the "new" V4 template and copy it over the equivalent file in your existing project.
* ***"Delete"***: Delete the file from your existing project. It is no longer required.
* ***"Add"***: Add the file to your existing project. It is a new file added in V4.

#### 1. Install the v4 template
Download and install the latest SAFE Stack V3 template by running the following command:

```bash
dotnet new -i SAFE.Template
```

#### 2. Create a v4 project
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

> **Important**! If you have installed extra dotnet tools, you will need to add them back in manually.

#### 5. Update global.json 

* **Overwrite** the `global.json` file

#### 6. Update Paket dependencies
* **Overwrite** the `paket.dependencies` file in the root of the solution.
* **Overwrite** the `paket.lock` file.
* **Overwrite** all `paket.references` files.

**Important** If you have installed extra NuGet packages, you will need to add them back in manually to the dependencies and references files.

Run paket to update project references:

```bash
dotnet paket install
```

* If you *have* added any extra NuGet packages, this command will also generate a new `paket.lock` file.

#### 7. Update the npm dependancies 
* **Overwite** the `package.json` file
* **Overwite** the `package-lock.json` file

**Important** If you have installed extra npm packages, you will need to add them back in manually to the dependencies.

#### 8. Update .gitignore 
* **Overwite** the `.gitignore` file in the root of the solution

#### 9. Update the build process
* **Overwite** the `Build.fs` file
* **Overwite** the `Build.fsproj` file

**Important** If you have made any modifications to the build script e.g. extra targets, you will need to add them back in manually.

#### 10. Update the webpack config
* **Overwrite** the `webpack.config.js` file.
* **Delete** the `webpack.tests.config.js` file

**Important** If you have made any modifications to the webpack file, you will need to apply them back in manually.

#### 11. Update TargetFramework in all projects
* **Update** the `Client.Tests.fsproj` file
* **Update** the `Server.Tests.fsproj` file
* **Update** the `Shared.Tests.fsproj` file
* **Update** the `Client.fsproj` file
* **Update** the `Server.fsproj` file
* **Update** the `Shared.fsproj` file

For all of the above, change
`<TargetFramework>net5.0</TargetFramework>`
to
`<TargetFramework>net6.0</TargetFramework>`

#### 12. Update the launch settings
* **Overwite** the `Server/Properties/launch.json` file

#### 13. Check that it runs
Run
```bash
dotnet run
```
at the root of the solution to launch the app and check everything is working as expected.

If you have problems loading your website, carefully check that you haven't missed out any javascript or nuget packages when overwriting the paket and package files. The console output will usually give you a good guide if this is the case.

#### Issues

On mac you might get an error like this:

```
> dotnet run
dotnet watch 🚀 Started
/Users/espen/code/dotnet-new-safe-4.1.1/.paket/Paket.Restore.targets(219,5): error MSB3073: The command "dotnet paket restore --project "/Users/espen/code/dotnet-new-safe-4.1.1/src/Shared/Shared.fsproj" --output-path "obj" --target-framework "net6.0"" exited with code 134. [/Users/espen/code/dotnet-new-safe-4.1.1/src/Shared/Shared.fsproj]

The build failed. Fix the build errors and run again.
dotnet watch ❌ Exited with error code 1
dotnet watch ⏳ Waiting for a file to change before restarting dotnet...
^Cdotnet watch 🛑 Shutdown requested. Press Ctrl+C again to force exit.
```

If so, try uninstalling all .NET SDKs and runtimes below 3.0.100. See [NET uninstall tool](https://learn.microsoft.com/en-us/dotnet/core/additional-tools/uninstall-tool?source=recommendations&tabs=macos) for how to unistall SDKs on mac.
