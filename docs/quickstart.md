This page provides some basic guidance on getting up and running with your first SAFE application.

## Install pre-requisites

You'll need to install the following pre-requisites in order to build SAFE applications

* The [.NET Core SDK 3.1](https://dotnet.microsoft.com/download/dotnet-core/)
* [node.js](https://nodejs.org/) (>= 8.0)
* [npm](https://www.npmjs.com/)
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) (optional - required for Azure deployments)

## Install an F# code editor

You'll also want an IDE to create F# applications. We recommend one of the following great IDEs:

* [VS Code](https://code.visualstudio.com/) + [Ionide](https://github.com/ionide/ionide-vscode-fsharp) extension
* [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [Jetbrains Rider](https://www.jetbrains.com/rider/)

## Create your first SAFE app

1. Create a new directory on your machine
2. Open a command prompt
3. Enter `dotnet new -i SAFE.Template` to install the [SAFE project template](template-overview.md)
4. Enter `dotnet new SAFE` to create a new SAFE project
5. Enter `dotnet tool restore` to install local tools like FAKE.
6. Enter `dotnet fake build --target run` to build and run the app

Congratulations - after a short delay, you'll be presented with a basic SAFE application running in your browser! The application will by default run in "development mode", which means it automatically watches your project for changes; whenever you save a file in the client project it will refresh the browser **automatically**; if you save a file in the server project it will also restart the server in the background.

The standard template creates an opinionated SAFE Stack app that contains everything you'll need to start developing, testing and deploying applications into Azure. Alternatively there is a "bare-bones" SAFE Stack app with minimal value-add features. Take a look at the [template options](template-overview.md#template-options) to see a side by side comparison of features available between the *standard* and *minimal* template.

## Troubleshooting
Still have issues getting started? Check out the [troubleshooting](faq-troubleshooting.md) page.
