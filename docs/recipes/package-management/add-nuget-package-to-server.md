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

> For a detailed explanation of package management using Paket, visit the official [docs](https://fsprojects.github.io/Paket/learn-how-to-use-paket.html).