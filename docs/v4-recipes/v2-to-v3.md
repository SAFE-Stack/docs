# How do I upgrade from SAFE v2 to v3?

There have been a number of changes between the second and third major versions of the SAFE template. This guide shows you how to upgrade your v2 project to v3.

> If you haven't done so already then you will need to install the prequisites listed in the [Quick Start](../../quickstart.md) guide.

### Terminology for this Recipe:

* ***"Overwrite"***: Take the file from the "new" V3 template and copy it over the equivalent file in your existing project.
* ***"Delete"***: Delete the file from your existing project. It is no longer required.
* ***"Add"***: Add the file to your existing project. It is a new file added in V3.

#### 1. Install the v3 template
Download and install the latest SAFE Stack V3 template by running the following command:

```bash
dotnet new install SAFE.Template::3.1.1
```

#### 2. Create a v3 project
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
* **Delete** the `build.fsx` FAKE script.
* **Add** the `Build.fs` file
* **Add** the `Helpers.fs` file
* **Add** the `Build.fsproj` project
* **Add** the `paket.references` file (the one directly under the root directory)
* Add the build project to the solution by running
  ```sh
  dotnet sln add Build.fsproj
  ```

**Important** If you have made any modifications to the build script e.g. extra targets, you will need to add them back in manually. You will also need to add any packages you added for the build to the paket.references file.

#### 10. Update the webpack config
* **Overwrite** the `webpack.config.js` file.
* **Overwrite** the `webpack.tests.config.js` file

**Important** If you have made any modifications to the webpack file, you will need to apply them back in manually.

* If you were using CSS files, make sure to follow the [Stylesheet recipe](../../recipes/ui/add-style.md) to add them back in.

#### 11. Update TargetFramework in all projects
* **Overwite** the `Client.fsproj`
* **Overwite** the `Server.fsproj`
* **Overwite** the `Shared.fsproj`

#### 12. Check that it runs
Run
```bash
dotnet run
```
at the root of the solution to launch the app and check everything is working as expected.

If you have problems loading your website, carefully check that you haven't missed out any JavaScript or NuGet packages when overwriting the paket and package files. The console output will usually give you a good guide if this is the case.




