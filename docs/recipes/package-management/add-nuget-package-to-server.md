# How do I add a Nuget package to the Server?

The way you add a Nuget package to the Server depends on whether you are using the Paket or Nuget package manager.

The minimal SAFE template uses Nuget by default, wheras the full template uses Paket.

---

## Adding a package using Nuget

1. Navigate to the **Server project** directory.

2. Run the following command to add a package to the project:

```bash
dotnet add package {name of package}
```

So for example to add FsToolkit, run
```bash
dotnet add package FsToolkit.ErrorHandling
```

Once you have done this, you will find an element in your fsproj file which looks like this:
```xml
<ItemGroup>
    <PackageReference Include="FsToolkit.ErrorHandling" Version="1.4.3" />
</ItemGroup>
```

You can also achieve the same thing using the [Visual Studio Package Manager](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio#nuget-package-manager), the [VS Mac Package Manager](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac) or the [Package Manager Console](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio#package-manager-console).

For a detailed explanation of package management using Nuget, visit the official [docs](https://docs.microsoft.com/en-us/nuget/consume-packages/overview-and-workflow).

## Adding a package using Paket

1. Navigate to the **root directory** of your solution and run

```bash
dotnet paket add {name of package} -p Server
```
So for example to add FsToolkit, run
```bash
dotnet paket add FsToolkit.ErrorHandling -p Server
```

This will add an entry to both the solution paket.dependencies file and the Server project's paket.reference file.