# How do I migrate to NuGet from Paket?

>  Note that the minimal template uses Nuget by default. **This recipe only applies to the full template**.

Paket is a fully featured package manager that acts as an alternative to the NuGet package manager commonly used in .NET.

It can help you reference libraries from NuGet, Git repositories or Http resources. It also provides precise control over your dependencies, separating direct and transitive references and capturing the exact configuration with each commit. You can find out more at the [Paket website](https://fsprojects.github.io/Paket/).

For most use cases, we would recommend sticking with Paket. If, however, you are in a position where you wish to remove it and revert back to the NuGet package manager, you can easily do so with the following steps.

#### 1. Remove Paket targets import from .fsproj files

In every project's `.fsproj` file you will find the following line. Remove it and save.

```xml
<Import Project="..\..\.paket\Paket.Restore.targets" />
```

#### 2. Remove paket.dependencies

You will find this file at the root of your solution. Remove it from your solution if included and then delete it.

#### 3. Add project dependencies via NuGet

Each project directory will contain a `paket.references` file. This lists all the NuGet packages that the project requires.

Inside a new `ItemGroup` in the project's `.fsproj` file you will need to add an entry for each of these packages.

> You can find the **version** of each package in the `paket.lock` file at the root of the solution.

```xml
<ItemGroup>
  <PackageReference Include="MyPackage1" Version="1.0.0" />
  <PackageReference Include="AnotherPackage" Version="2.0.1" />
  <!--...add entry for each package in the references file...-->
</ItemGroup>
```

#### 4. Remove remaining paket files

Once you have added all of your dependencies to the relevant `.fsproj` files, you can remove `paket.lock` and all of the `paket.references` files from your solution if included and delete them.

#### 5. Remove paket tool

If you open `.config/dotnet-tools.json` you will find an entry for paket. Remove it.

Alternatively, run 

```bash
dotnet tool uninstall paket
```
at the root of your solution.