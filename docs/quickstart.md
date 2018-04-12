This page provides some basic guidance on getting up and running with your first SAFE application.

## Install pre-requisites
You'll need to install the following pre-requisites in order to build SAFE applications

* The [.NET Core SDK 2.x](https://www.microsoft.com/net/download/).
* The [Yarn](https://yarnpkg.com/lang/en/docs/install/) package manager.
* [Node 8.x](https://nodejs.org/en/download/) installed for the front end components.
* If you're running on OSX or Linux, you'll also need to install [Mono](https://www.mono-project.com/docs/getting-started/install/).

## Install an F# code editor
You'll also want an IDE to create F# applications. We recommend one of the following great IDEs.

* [VS Code](https://code.visualstudio.com/) + [Ionide](https://github.com/ionide/ionide-vscode-fsharp) extension
* [Visual Studio 2017](https://www.visualstudio.com/downloads/)
* [Jetbrains Rider](https://www.jetbrains.com/rider/)

## Create your first SAFE app
1. Create a new directory on your machine
2. Open a command prompt
3. Enter `dotnet new -i SAFE.Template` to install the [SAFE project template](https://github.com/SAFE-Stack/SAFE-template) (you only need to do this once!)
4. Enter `dotnet new SAFE -lang F#` to create a new SAFE project
5. Enter `build.cmd run` (Windows) or `./build.sh run` (Linux / OSX)

Congratulations! After a short delay, you'll be presented with a SAFE application running in your browser! 

The app is already running in development mode and watches your project for changes. Whenever you save a file in the client project it will refresh the browser automatically. If you save a file in the server project it will restart the server in the background.

Tip: Take a look at the [template options](https://github.com/SAFE-Stack/SAFE-template#template-options). There are many predefined options to choose from, like server technology or client/server communication technology.
