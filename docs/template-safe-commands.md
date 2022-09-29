The SAFE Stack now runs FAKE using a console app rather than a script.

## **"Run"**
```powershell
dotnet run
```

Used for development purposes, and provides a great live-reload experience. It pulls down any dependencies required for both the client and server, before running both the client and server in a "watch" mode, so any changes you make on either side will be automatically applied without your needing to restart the application.

> Navigating to `http://localhost:8080/` will load the application.

## **"Bundle"** target
```powershell
dotnet run Bundle
```

Used to both build and package up your application in a production fashion, ready for deployment. It will restore all dependencies and build both the client and server in a production and release mode respectively, and correctly copy the outputs into the `deploy` folder in the root of the application. Once your build has completed, you can launch the entire application locally to test it as follows:

```powershell
cd deploy
Server
```

> Navigating to `http://localhost:5000/` will load the application.

## **"Azure"** target
```powershell
dotnet run Azure
```

This target will deploy your application to Azure with a fully configured Application Insights instance. **You do not need to pre-create any resources in Azure** - the template will create everything needed, using free SKUs so you can test without any costs.

> You must already have an Azure account and will be prompted to log into it during the deployment process.

This build step uses both the [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) and [Farmer](https://compositionalit.github.io/farmer/) projects to create all resources in just a few lines of code.

> The name of resources will be generated based on the folder in which you created the application. These may be incompatible with Azure naming rules, or may already be in use (Azure web applications must be globally unique) so you may have to modify the name of the webapp to pick one that is acceptable.

## **"RunTests"** target
```powershell
dotnet run RunTests
```

This target behaves similarly to the standard Run target, except that it launches the unit tests for both client and server.

* The server tests will run immediately in the console, using watch mode to allow you to rapidly iterate on your tests.
* The client tests run *in the browser*. Again, they use a watch mode so you can make changes to your client code and see the results in the browser.

> Launch the client tests on `http://localhost:8081/`

## **"Format"** target
```powershell
dotnet run Format
```

This target will format all the F# files in the `src` folder using [Fantomas](https://github.com/fsprojects/fantomas). Out of the box, Fantomas tries to reformat the code according to the [F# style guide by Microsoft](https://docs.microsoft.com/en-us/dotnet/fsharp/style-guide/formatting). For more info, check out [the documentation](https://github.com/fsprojects/fantomas/blob/master/docs/Documentation.md).
