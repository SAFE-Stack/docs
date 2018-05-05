The [SAFE Template](https://github.com/SAFE-Stack/SAFE-template) is a [dotnet CLI template](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-new?tabs=netcore2x) for SAFE Stack projects, designed to get you up and running as quickly as possible, with flexible options to suit your application.

The template gets you up and running with the most common elements of the stack:

* [Saturn](https://saturnframework.github.io/docs/), [Giraffe](https://github.com/giraffe-fsharp/Giraffe) or [Suave](https://suave.io/) for your web server
* [Fable](http://fable.io/) for client-side F#
* [Elmish](https://fable-elmish.github.io/elmish/) for web UI
* [Fulma](https://mangelmaxime.github.io/Fulma/) for consistent web styling
* [Docker](template-docker.md) or [Azure App Service](template-appservice.md) deployment models for hosting.

## Using the template
Refer to the [Quick Start guide](quickstart.md#create-your-first-safe-app) to see basic guidance on how to install and use the template.

## Template options
The template provides the ability to customise the created application. You can see template version and available options by running  `dotnet new SAFE --help`.

### Server
*Configures the SAFE app to use one of three different back-end hosting platforms.*

Usage: `dotnet new SAFE --Server <server>`

Where `<server>` is one of:

* `saturn`: Creates a SAFE app running on Saturn on top of Giraffe **(default)**.
* `giraffe`: Creates a SAFE app running on Giraffe only.
* `suave`: Creates a SAFE app running on Suave.

### Fulma
*Configures the SAFE app to apply [Fulma](https://mangelmaxime.github.io/Fulma) bindings to the UI template.*

Usage: `dotnet new SAFE --Fulma <template>`

Where `<template>` is one of:

* `none`: don't add Fulma bindings at all **(default)**.
* `basic`: adds Fulma basic template.
* `admin`: adds Fulma with the ['Admin'](https://dansup.github.io/bulma-templates/templates/admin.html) Bulma template.
* `cover`: adds Fulma with the ['Cover'](https://dansup.github.io/bulma-templates/templates/cover.html) Bulma template.
* `hero`: adds Fulma with the ['Hero'](https://dansup.github.io/bulma-templates/templates/hero.html) Bulma template.
* `landing`: adds Fulma with the ['Landing'](https://dansup.github.io/bulma-templates/templates/landing.html) Bulma template.
* `login`: adds Fulma with the ['Login'](https://dansup.github.io/bulma-templates/templates/login.html) Bulma template.

### Remoting
*Configures the SAFE app to use [Fable.Remoting](https://github.com/Zaid-Ajaj/Fable.Remoting) to the template. If this argument is not supplied, client / server communication will be handled through the standard routing and serialization mechanism of the server. See [here](feature-clientserver.md) for an overview of Fable Remoting and the alternatives for sharing data between client and server.*

Usage: `dotnet new SAFE --Remoting`

### Deploy
*Optionally configures the SAFE app to elements needed for deploying to one of two different hosting models. If this argument is not supplied, no explicit support for any hosting model will be provided.*

Usage: `dotnet new SAFE --Deploy <hosting model>`

Where `<hosting model>` is one of:

* `docker`: Adds [FAKE](https://fake.build/) targets that bundles and build a Docker image. See [here](template-docker.md) for more details about Docker deployment.
* `azure`: Adds [FAKE](https://fake.build/) targets and an [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) (ARM) template that allows deployment to the [Azure App Service](https://azure.microsoft.com/en-us/services/app-service/) plus an [Azure Application Insights](https://azure.microsoft.com/en-us/services/application-insights/) instance. See [here](template-appservice.md) for more details about Azure deployment.

### NPM
*Configures the SAFE app to use NPM instead of default Yarn for JS package management.*

Usage: `dotnet new SAFE --NPM`

## Examples
* Create a SAFE application using all defaults: `dotnet new SAFE -lang F#`
* Create a SAFE application using giraffe with Fable Remoting: `dotnet new SAFE -lang F# --Server giraffe --Remoting`
* Create a SAFE application using Suave with Fulma: `dotnet new SAFE -lang F# --Server suave --Fulma landing`
