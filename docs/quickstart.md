This page provides some basic guidance on getting up and running with your first SAFE application.

## Install pre-requisites

You'll need to install the following pre-requisites in order to build SAFE applications

* The [.NET 6 SDK](https://dotnet.microsoft.com/download/dotnet/6.0)
* [node.js](https://nodejs.org/) (v16.x)
* [npm](https://www.npmjs.com/) (v8.x)
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) (optional - required for Azure deployments)

## Install an F# code editor

You'll also want an IDE to create F# applications. We recommend one of the following great IDEs:

* [VS Code](https://code.visualstudio.com/) + [Ionide](https://github.com/ionide/ionide-vscode-fsharp) extension
* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Jetbrains Rider](https://www.jetbrains.com/rider/)

## Create your first SAFE app

1. Open a command prompt
1. Create a new directory on your machine and navigate into it
1. Enter `dotnet new -i SAFE.Template` to install the [SAFE project template](template-overview.md) (*only required once *)
1. Enter `dotnet new SAFE` to create a new SAFE project
1. Enter `dotnet tool restore` to install local tools like Fable.
1. Enter `dotnet run` to build and run the app
1. Open a web browser and navigate to http://localhost:8080.

Congratulations - after a short delay, you'll be presented with a basic SAFE application running in your browser! The application will by default run in "development mode", which means it automatically watches your project for changes; whenever you save a file in the client project it will refresh the browser **automatically**; if you save a file in the server project it will also restart the server in the background.

The standard template creates an opinionated SAFE Stack app that contains everything you'll need to start developing, testing and deploying applications into Azure. Alternatively there is a "bare-bones" SAFE Stack app with minimal value-add features. Take a look at the [template options](template-overview.md#template-options) to see a side by side comparison of features available between the *standard* and *minimal* template.

## Troubleshooting
Still have issues getting started? Check out the [troubleshooting](faq-troubleshooting.md) page.
