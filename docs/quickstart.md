This page provides some basic guidance on getting up and running with your first SAFE application.

## Install pre-requisites

You'll need to install the following pre-requisites in order to build SAFE applications

* [dotnet SDK](https://dotnet.microsoft.com/download) (>= 2.2)
* [FAKE](https://fake.build/) (>= 5.12) installed as global tool (`dotnet tool install -g fake-cli`)
* (optional) [Paket](https://fsprojects.github.io/Paket) (>= 5.196) installed as global tool (`dotnet tool install -g paket`)
* [node.js](https://nodejs.org/) (>= 8.0)
* [yarn](https://yarnpkg.com/) (>= 1.10.1) or [npm](https://www.npmjs.com/)

## Install an F# code editor

You'll also want an IDE to create F# applications. We recommend one of the following great IDEs.

* [VS Code](https://code.visualstudio.com/) + [Ionide](https://github.com/ionide/ionide-vscode-fsharp) extension (support for [Full Stack Debugging](feature-debugging.md))
* [Visual Studio 2017](https://www.visualstudio.com/downloads/)
* [Jetbrains Rider](https://www.jetbrains.com/rider/)

## Create your first SAFE app

1. Create a new directory on your machine
2. Open a command prompt
3. Enter `dotnet new -i SAFE.Template` to install the [SAFE project template](template-overview.md)
4. Enter `dotnet new SAFE` to create a new SAFE project
5. Enter `fake build --target run` to build and run the app

Congratulations - after a short delay, you'll be presented with a basic SAFE application running in your browser! The application will by default run in "development mode", which means it automatically watch your project for changes; whenever you save a file in the client project it will refresh the browser **automatically**; if you save a file in the server project it will also restart the server in the background.

Take a look at the [template options](template-overview.md#template-options). There are several ways to customise the default application, such as server and client/server communication technologies.

## Troubleshooting
Still have issues getting started? Check out the [troubleshooting](faq-troubleshooting.md) page.