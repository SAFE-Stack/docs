# How do I migrate to Paket from NuGet?
Paket is a fully featured package manager that acts as an alternative to the NuGet package manager.

It can help you reference libraries from NuGet, Git repositories or Http resources.

It also provides precise control over your dependencies, separating direct and transitive references and capturing the exact configuration with each commit.

You can find out more at the [Paket website.](https://fsprojects.github.io/Paket/)


---

### How do I install Paket?

1. Install the Paket dotnet tool.

This can be done globally or per-project. We would recommend installing per-project. This means that you will include a tool manifest with your source code. The manifest allows others to easily install the tools required to build your application.

2. If you don't already have a tool manifest, at the root of your codebase run:
```
dotnet new tool-manifest
```

3. Run these commands to install and restore the Paket tool:
```
dotnet tool install paket
dotnet tool restore
```

### How do I migrate my existing Nuget references?

Run this command to move existing references to Paket from your packages.config or .fsproj file:
```
dotnet paket convert-from-nuget
```

If you are working in Visual Studio, you will need to add to your Solution both the paket.lock file created in your base directory and any paket.references files created in your project directories during the last step if you wish to see them in the Explorer window.

For a more detailed explanation of this process see the official [migration guide.](https://fsprojects.github.io/Paket/convert-from-nuget-tutorial.html)
