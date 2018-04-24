The template uses [FAKE](https://fake.build/) to build the application.

Generated FAKE script consist of two primary build chains:

1. **Build**
2. **Run**

## Build target

This target is a standard build procedure, consisting of following steps:

1. **InstallDotNetCore** - here, a required version of dotnet core is read from `global.json` file and if not yet installed, the script downloads and performs the installation of desired dotnet core SDK,
1. **InstallClient** - in this step, either NPM or Yarn is invoked (based on which option was chosen when generating project) and it installs all Client dependencies defined in `package.json`. The step also performs `dotnet restore` to fetch NuGet-based packages used by front-end,
1. **Build** - for server side `dotnet build` command is invoked, and for client side a special `dotnet-fable` CLI tool using `dotnet fable webpack` command with `-p` flag to compile Client project to a single JavaScript bundle file,
1. **Bundle** (only if `--Docker` flag specified) - all necessary artifacts for both Server and Client are collected for following `Docker` target,
1. **Docker** (only if `--Docker` flag specified) - based on present `Dockerfile`, docker image is built and tagged using `dockerUser` and `dockerImageName` values from the script.

## Run target

This target is used for development purposes, and provides a great live-reload experience. It consists of following steps:

1. **InstallClient** - same as above in `Build` chain,
1. **RestoreServer** - `dotnet restore` is invoked for Server to fetch all necessary packages (note `dotnet build` is skipped here),
1. **Run** - most interesting part; in this step 3 separate actions are performed in parallel:
  1. `dotnet watch run` for Server side - compiles, runs Server and watches for changes in Server source files. Whenever a change in any source file is detected, Server is automatically stopped, recompiled and rerun in the background,
  1. `dotnet fable webpack-dev-server` for Client side - compiles Client to JavaScript and runs [Webpack dev-server](https://github.com/webpack/webpack-dev-server) - this in turn recompiles and reloads the client application upon any source file change in Client project,
  1. New process is started for `http://localhost:8080` to open the URL in default browser