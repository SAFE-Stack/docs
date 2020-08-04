# How do I migrate to Paket from NuGet?
Paket is a fully featured package manager that acts as an alternative to the NuGet package manager.

It can help you reference libraries from NuGet, Git repositories or Http resources. It also provides precise control over your dependencies, separating direct and transitive references and capturing the exact configuration with each commit. You can find out more at the [Paket website.](https://fsprojects.github.io/Paket/)


---

#### 1. Create a .NET Tool Manifest
The manifest allows others to easily install the tools required to build your application. At the root of your codebase run:
```bash
dotnet new tool-manifest
```

#### 2. Install and restore Paket
```bash
dotnet tool install paket
dotnet tool restore
```

This will add three types of file to your solution, all of which should be commited to source control:

- [paket.dependencies](https://fsprojects.github.io/Paket/dependencies-file.html): This will be at the solution root and contains the top level list of dependencies for your project. It is also used to specify any rules such as where they should be downloaded from and which versions etc.
- [paket.lock](https://fsprojects.github.io/Paket/lock-file.html): This will also be at the solution root and contains the concrete resolution of all direct and transitive dependencies.
- [paket.references](https://fsprojects.github.io/Paket/references-files.html): There will be one of these in each project directory. It simply specifies which packages the project requires.

#### 3. Run the Migration
Run this command to move existing Nuget references to Paket from your packages.config or .fsproj file:
```bash
dotnet paket convert-from-nuget
```

For a more detailed explanation of this process see the official [migration guide.](https://fsprojects.github.io/Paket/convert-from-nuget-tutorial.html)

> In the case where you have added a nuget project to a solution which is already using paket, run this command with the option `--force`.

> If you are working in Visual Studio and wish to see your Paket files in the Solution Explorer, you will need to add both the paket.lock and any paket.references files created in your project directories during the last step to your solution.
