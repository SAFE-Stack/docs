The SAFE template has the ability to easily deploy to a Docker container. The details of the additions to the FAKE script are shown here.

## Custom FAKE build tasks

1. **Bundle** - all necessary artifacts for both Server and Client are collected for following `Docker` target.
1. **Docker** - based on present `Dockerfile`, docker image is built and tagged using `dockerUser` and `dockerImageName` values from the script. 

## Docker image

The image is based on [microsoft/dotnet:runtime](https://hub.docker.com/r/microsoft/dotnet/).
Entrypoint for the image is `dotnet Server.dll` (with `/Server` working directory).
To allow incoming traffic, port 8085 is exposed.

> Note: Before running the `Docker` target, make sure to modify the default `dockerUser` and `dockerImageName` values in script.

You can also [deploy to the Azure App Service platform](template-appservice.md).