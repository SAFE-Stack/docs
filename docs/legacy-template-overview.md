The [SAFE Template](https://github.com/SAFE-Stack/SAFE-template) is a [dotnet CLI template](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-new) for SAFE Stack projects, designed to get you up and running as quickly as possible, with flexible options to suit your application. The template gets you up and running with the most common elements of the stack with minimal configuration options.

All template options come with a fully working end-to-end SAFE application with known-good dependencies on client (NPM) and server (NuGet), as well as a preconfigured webpack configuration file.

## Using the template
Refer to the [Quick Start guide](quickstart.md#create-your-first-safe-app) to see basic guidance on how to install and use the template.

## Template options
The template provides two simple modes: the *standard* and *minimal* template.

### Standard Template
The standard template creates an opinionated SAFE Stack app that contains everything you'll need to start developing, testing and deploying applications into Azure.

```powershell
dotnet new SAFE
```

**Use this configuration if..**

* .. you are brand new to SAFE Stack, or F#, or software development in general, and want a "recommended" experience
* .. you want to get up and running as quickly as possible
* .. you are an F# developer and want an experience that uses tools that you are familiar with

### Minimal Template
The minimal template is a "bare-bones" SAFE Stack app with minimal value-add features.

```powershell
dotnet new SAFE -m
```

**Use this configuration if..**

* .. you are a SAFE Stack expert and want to hand-craft your own SAFE Stack application from a minimal starting point
* .. you are coming from a web development background and know your way around tools like NPM and Webpack
* .. you are comfortable creating your own build and packaging pipeline
* .. you want to see "behind the magic" and get a feel for what is happening behind the scenes


### At-a-glance Comparison

| Feature | Standard | Minimal |
|-:|:-:|:-:|
| Styling | Fulma + Bulma | None |
| Starter App | Todo List | None |
| Communication | Fable Remoting | Raw HTTP |
| .NET Package Manager | Paket | NuGet |
| Build Tooling | FAKE | None |
| Azure Integration | Farmer | None |
| Testing Support | Client and Server | None |
| Recommendations | VS Code Extensions, Code Style | No |