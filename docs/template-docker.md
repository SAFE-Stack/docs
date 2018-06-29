The SAFE template has the ability to easily create a [Docker](https://www.docker.com/) container. The details of the additions to the FAKE script are shown here. For this deployment option SAFE uses the Linux containers and therefore you need docker installed on your machine and configure it to run with [Linux containers](https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/linux-containers).

## Custom FAKE build tasks

1. **Bundle** - all necessary artifacts for both Server and Client are collected for following `Docker` target.
1. **Docker** - based on present `Dockerfile`, docker image is built and tagged using `dockerUser` and `dockerImageName` values from the script. 

## Docker image

The image is based on [microsoft/dotnet:runtime](https://hub.docker.com/r/microsoft/dotnet/).
Entrypoint for the image is `dotnet Server.dll` (with `/Server` working directory).
To allow incoming traffic, port 8085 is exposed.

> Note: Before running the `Docker` target, make sure to modify the default `dockerUser` and `dockerImageName` values in script.

## Release to Azure App Service

The following part shows how to set up automatic deployment to [Microsoft Azure](https://azure.microsoft.com).

### Docker Hub

Create a new [Docker Hub](https://hub.docker.com) account and a new public repository on Docker Hub.

### Release script

Create a file called `release.cmd` with the following content and configure your DockerHub credentials:

    @echo off
    cls

    fake build --target Deploy "DockerLoginServer=docker.io" "DockerImageName=****" "DockerUser=****" "DockerPassword=***" %*

Don't worry the file is already in `.gitignore` so your password will not be commited.

### Initial docker push

In order to release a container you need to create a new entry in [RELEASE_NOTES.md] and run `release.cmd`.
This will build the server and client, run all test, put the app into a docker container and push it to your docker hub repo.

### Azure Portal

Go to the [Azure Portal](https://portal.azure.com) and create a new "Web App for Containers".
Configure the Web App to point to the docker repo and select `latest` channel of the container.

![Docker setup](https://user-images.githubusercontent.com/57396/31279587-e06001d0-aaa9-11e7-9b4b-a3e8278a6419.png)

Also look for the "WebHook Url" on the portal, copy that url and set it as new trigger in your Docker Hub repo.

*Note that entering a Startup File is not necessary.*

The `Dockerfile` used to create the docker image exposes port 8085 for the Giraffe server application. This port needs to be mapped to port 80 within the Azure App Service for the application to receive http traffic.

Presently this can only be done using the Azure CLI. You can do this easily in Azure Cloud Shell (accessible from the Azure Portal in the top menu bar) using the following command:

`az webapp config appsettings set --resource-group <resource group name> --name <web app name> --settings WEBSITES_PORT=8085`

The above command is effectively the same as running `docker run -p 80:8085 <image name>`.

Now you should be able to reach the website on your `.azurewebsites.net` url.

### Further releases

Now everything is set up. By creating new entries in [RELEASE_NOTES.md] and a new run of `release.cmd` the website should update automatically.

Alternatively, you can use the [ARM delpoy option](template-appservice.md) to do automatic deployment to the Azure App Service platform.
