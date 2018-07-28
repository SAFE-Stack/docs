## Going serverless with SAFE

With SAFE-Stack you can easily take advantage of serverless computing via [Azure Functions](https://azure.microsoft.com/en-us/services/functions/).

With Functions-As-A-Service (FAAS) you can focus on building your business logic and don't need to worry about provisioning and maintaining servers (hence "serverless"). Azure Functions provides a managed compute platform with high reliability. If you use a "consumption plan" it scales on demand and you only get billed for the actual runtime of your code.

### Potential use cases

For SAFE apps we see various use cases for FAAS:

* Running recurring jobs to create statistics or maintenance actions via timer triggers
* Running jobs that can be processed async like creating accountings or sending email 
* Command processing in CQRS apps via message queues or HTTP triggers


## Editing Functions in the Azure Portal

The [Azure Portal](https://portal.azure.com) allows you to create and edit Functions and their source code via an online editor. While it seem very convenient, this should only be used for testing and prototyping. In SAFE-Stack you usually benefit from reusing your domain model at various places [see Client/Server](feature-clientserver.md) - so we recommend to use "precompiled Azure Functions" as described below.

## Deployment

In SAFE-Stack scenarios we recommend all deployments should be automated. In the case of Function Apps the excellent [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) can be used. If you use core tools version 2 then the following should be added to your build/deploy script:

    dotnet publish -c Release
    func azure functionapp publish [FunctionApp Name]

This will compile your Function App in release mode and push it to the Azure portal.