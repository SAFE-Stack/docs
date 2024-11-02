## Going serverless with SAFE

With SAFE-Stack you can easily take advantage of serverless computing via [Azure Functions](https://azure.microsoft.com/en-us/services/functions/).

With Functions-As-A-Service (FAAS) you can focus on building your business logic and don't need to worry about provisioning and maintaining servers (hence "serverless"). Azure Functions provide a managed compute platform with high reliability. If you use a "consumption plan" it scales on demand and you only get billed for the actual runtime of your code.

### Potential use cases

For SAFE apps we see various use cases for FAAS:

* Running recurring jobs to create statistics or maintenance actions via timer triggers
* Running jobs that can be processed async like creating accountings or sending email 
* Command processing in CQRS apps via message queues or HTTP triggers


## Editing Functions in the Azure Portal

The [Azure Portal](https://portal.azure.com) allows you to create and edit Functions and their source code via an online editor. 

For a short test go to the portal, click the "New" button and search for "Function App". Click through the wizard to create a new Function App. Open the app when it's created and add a new function. Pick "Timer" as the scenario and F# as the language.

Replace the contents of function.json with:

    {
    "bindings": [
        {
        "name": "myTimer",
        "type": "timerTrigger",
        "direction": "in",
        "schedule": "0 * * * * *"
        }
    ],
    "disabled": false
    }

and replace the run.fsx with the following F# code:

    open System

    let minutesSince (d: DateTime) =
    (DateTime.Now - d).TotalMinutes

    let run(myTimer: TimerInfo, log: TraceWriter) =
    let meetupStart = new DateTime(2017, 11, 8, 19, 0, 0)

    minutesSince meetupStart
    |> int
    |> sprintf "Our meetup has been running for %d minutes"
    |> log.Info

Now observe the logs to see that the function runs every minute and outputs the message about the meetup duration.

While it seems very convenient, the online editor should only be used for testing and prototyping. In SAFE-Stack you usually benefit from reusing your domain model at various places [see Client/Server](feature-clientserver.md) - so we recommend using "precompiled Azure Functions" as described below.

## Deployment
In SAFE-Stack scenarios we recommend all deployments should be automated. Here, we discuss two options for deploying your functions apps into Azure.

### Azure Functions Core Tools
In the case of Function Apps the excellent [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) can be used. If you use core tools version 2 then the following should be added to your build/deploy script:

    dotnet publish -c Release
    func azure functionapp publish [FunctionApp Name]

This will compile your Function App in release mode and push it to the Azure portal.

In the case of a CI server etc., you will need to install the Functions Core Tools on the server and once per functions app log into the CI machine and explicitly authenticate it manually (see the Functions Core Tools docs).

### HTTPS Upload
Since Azure Functions sits on top of Azure App Service, the same mechanisms for deployment there also exist here. In this case, you can use the exact same HTTPS upload capabilities of the App Service to upload a zip of your functions app into your Functions app. The standard SAFE Template can generate this for you for the core SAFE application as part of the FAKE script; the exact same mechanism can be utilised for your functions app.

As per the standard App Service, HTTPS upload uses a user/pass supplied in the header of the zip which is PUT into the functions app. This user / pass can be taken from the App Service in the Azure Portal directly, or extracted during the deployment of your ARM template (as per the FAKE script does for the App Service).
