# How do I add a NuGet package to the Server?
You can add NuGet packages to the server to give it more capabilities. You can download a wide variety of packages from [the official NuGet site](https://nuget.org/).

In this example we will add the [FsToolkit ErrorHandling package](https://www.nuget.org/packages/FsToolkit.ErrorHandling/) package.

---

## **I'm using the standard template** (Paket)

#### 1. Add the package
Navigate to the **root directory** of your solution and run:

```bash
dotnet paket add FsToolkit.ErrorHandling -p Server
```

This will add an entry to both the solution [paket.dependencies](https://fsprojects.github.io/Paket/dependencies-file.html) file and the Server project's [paket.reference](https://fsprojects.github.io/Paket/references-files.html) file, as well as update the lock file with the updated dependency graph.

> Find information on how you can convert your project from NuGet to Paket [here](migrate-to-paket.md).
>
> For a detailed explanation of package management using Paket, visit the official [docs](https://fsprojects.github.io/Paket/learn-how-to-use-paket.html).

## **I'm using the minimal template** (NuGet)
#### 1. Navigate to the **Server** project directory
#### 2. Add the package
Run the following command:

```bash
dotnet add package FsToolkit.ErrorHandling
```

Once you have done this, you will find an element in your fsproj file which looks like this:
```xml
<ItemGroup>
    <PackageReference Include="FsToolkit.ErrorHandling" Version="1.4.3" />
</ItemGroup>
```

> You can also achieve the same thing using the [Visual Studio Package Manager](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio#nuget-package-manager), the [VS Mac Package Manager](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac) or the [Package Manager Console](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio#package-manager-console).

> For a detailed explanation of package management using NuGet, visit the official [docs](https://docs.microsoft.com/en-us/nuget/consume-packages/overview-and-workflow).
