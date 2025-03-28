# How do I opt out of Meta packages?

By default, projects created using the SAFE template depend on the SAFE.Meta packages. This package provides functionality, but mainly exists to simplify dependency management; a standard SAFE app might very well exist with only two dependencies, being the SAFE.Client and SAFE.Server packages.

If your app starts deviating a lot from the standard SAFE app, you might want to uninstall the SAFE.Meta packages, so that you can remove packages it pulls in. This recipe explains how to do so.

## 1. Add the SAFE dependencies explicitly to your SAFE app.

Add the packages that SAFE.Meta relies on as explicit dependencies:

> Instead of using the snippets here, you can also copy these sections from the [SAFE.Meta repository](https://github.com/SAFE-Stack/SAFE.Meta), to make sure you have the most up-to-date versions

```diff title="paket.dependencies"
  source https://api.nuget.org/v3/index.json
  framework: net8.0
  storage: none
  
  nuget Expecto ~> 9
 
- nuget SAFE.Server ~> 5
- nuget SAFE.Client ~> 5
+ nuget FSharp.Core ~> 8
+ 
+ nuget Fable.Remoting.Giraffe ~> 5
+ nuget Saturn ~> 0
+ 
+ nuget Fable.Core ~> 4
+ nuget Fable.Elmish ~> 4
+ nuget Fable.Elmish.React ~> 4
+ nuget Fable.Elmish.HMR ~> 7
+ nuget Fable.Mocha ~> 2
+ nuget Fable.Remoting.Client ~> 7
+ nuget Fable.SimpleJson
+ nuget Feliz ~> 2
  
  nuget Fake.Core.Target ~> 5
  nuget Fake.IO.FileSystem ~> 5
  nuget Farmer ~> 1
```

```diff title="src/Client/paket.references"
- SAFE.Client
+ FSharp.Core
+ Fable.Core
+ Fable.Elmish
+ Fable.Elmish.React
+ Fable.Elmish.HMR
+ Fable.Mocha
+ Fable.Remoting.Client
+ Feliz
+ Fable.SimpleJson
```

```diff title="src/Server/paket.references"
- Safe.Server
+ FSharp.Core
+ Saturn
+ Fable.Remoting.Giraffe
```

And re-install the paket dependencies

```bash
dotnet paket install
```

This will remove the Meta packages from paket.lock, and may, depending on how up-to-date your project is, update any out-of-date packages. 

```diff title="paket.lock"
  ...
-     SAFE.Client (5.1.1)
-       Fable.Core (>= 4.5 < 5.0)
-       Fable.Elmish (>= 4.2 < 5.0)
-       Fable.Elmish.HMR (>= 7.0 < 8.0)
-       Fable.Elmish.React (>= 4.0 < 5.0)
-       Fable.Mocha (>= 2.17 < 3.0)
-       Fable.Remoting.Client (>= 7.32 < 8.0)
-       Fable.SimpleJson (>= 3.24)
-       Feliz (>= 2.9 < 3.0)
-       FSharp.Core (>= 8.0.403 < 9.0)
-     SAFE.Server (5.1.1)
-       Fable.Remoting.Giraffe (>= 5.21 < 6.0)
-       FSharp.Core (>= 8.0.403 < 9.0)
-       Saturn (>= 0.17 < 1.0)
  ...
```

## 2. Copy SAFE.Meta functionality into your own project

If you are using any of the code supplied by the Meta packages, you need to copy the code into your own project:

Navigate to the [SAFE.Meta repository](https://github.com/SAFE-Stack/SAFE.Meta) and copy the following files into your repository:

* src/SAFE.Client/SAFE.fs -> src/Client/SAFE.fs
* src/SAFE.Server/SAFE.fs -> src/Server/SAFE.fs

and update the corresponding project files:

```diff title="Client.fsproj"
  <?xml version="1.0" encoding="utf-8"?>
  <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
          <TargetFramework>net8.0</TargetFramework>
          <DefineConstants>FABLE_COMPILER</DefineConstants>
      </PropertyGroup>
      <ItemGroup>
          <None Include="postcss.config.js" />
          <None Include="tailwind.config.js" />
          <None Include="index.html" />
          <None Include="paket.references" />
+         <Compile Include="SAFE.fs" />
          <Compile Include="Index.fs" />
          <Compile Include="App.fs" />
          <None Include="vite.config.mts" />
      </ItemGroup>
      <ItemGroup>
          <ProjectReference Include="..\Shared\Shared.fsproj" />
      </ItemGroup>
      <Import Project="..\..\.paket\Paket.Restore.targets" />
  </Project>
```

```diff title="Server.fsproj"
  <?xml version="1.0" encoding="utf-8"?>
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
      <OutputType>Exe</OutputType>
      <TargetFramework>net8.0</TargetFramework>
      <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
    </PropertyGroup>
    <ItemGroup>
      <None Include="paket.references" />
+     <Compile Include="SAFE.fs" />
      <Compile Include="Server.fs" />
    </ItemGroup>
    <ItemGroup>
      <ProjectReference Include="..\Shared\Shared.fsproj" />
    </ItemGroup>
    <Import Project="..\..\.paket\Paket.Restore.targets" />
  </Project>
```

## 3. Use Paket to remove any redundant dependencies

You're now able to remove any dependencies you do not need with Paket.

```bash
dotnet paket remove <package-name>
```