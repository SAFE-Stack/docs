# How do I add a Nuget package to the Client?

Adding packages to the Client project is a very [similar process to the Server](../add-nuget-package-to-server), with one important difference.

Although the Client code is written in F#, at runtime it is converted into Javascript using [Fable](https://fable.io/docs/index.html). 

Because of this, we must be careful to only reference libraries which are [Fable compatible](https://fable.io/docs/your-fable-project/use-a-fable-library.html).

There are [lots of great libraries](https://www.nuget.org/packages?q=Tags%3A%22fable%22) available to choose from.

The way you add a Nuget package to the Client depends on whether you are using the Paket or Nuget package manager.

The _minimal_ SAFE template uses Nuget by default, wheras the _full_ template uses Paket.

---

## Adding a package using Nuget

1. Navigate to the **Client** project directory.

2. Run the following command to add a package to the project:

```bash
dotnet add package {name of package}
```

So for example, to add Thoth.Json you should run
```bash
dotnet add package Thoth.Json
```

Once you have done this, you will find an element in your fsproj file which looks like this:
```xml
<ItemGroup>
    <PackageReference Include="Thoth.Json" Version="4.1.0" />
</ItemGroup>
```

You can also achieve the same thing using the [Visual Studio Package Manager](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio#nuget-package-manager), the [VS Mac Package Manager](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac) or the [Package Manager Console](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio#package-manager-console).

For a detailed explanation of package management using Nuget, visit the official [docs](https://docs.microsoft.com/en-us/nuget/consume-packages/overview-and-workflow).


## Adding a package using Paket

1. Navigate to the **root directory** of your solution and run

```bash
dotnet paket add {name of package} -p Client
```
So for example to add Thoth.Json, run
```bash
dotnet paket add Thoth.Json -p Client
```

This will add an entry to both the solution [paket.dependencies](https://fsprojects.github.io/Paket/dependencies-file.html) file and the Client project's [paket.reference](https://fsprojects.github.io/Paket/references-files.html) file.

Find information on how you can convert your project from nuget to Paket [here](../migrate-to-paket).

For a detailed explanation of package management using Paket, visit the official [docs](https://fsprojects.github.io/Paket/learn-how-to-use-paket.html).