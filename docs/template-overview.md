The [SAFE Template](https://github.com/SAFE-Stack/SAFE-template) is a [dotnet CLI template](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-new?tabs=netcore2x) for SAFE Stack projects, designed to get you up and running as quickly as possible, with flexible options to suit your application.

The template gets you up and running with 3 core components of the stack:

* [Saturn](https://saturnframework.github.io/docs/), [Giraffe](https://github.com/giraffe-fsharp/Giraffe) or [Suave](https://suave.io/)
* [Fable](http://fable.io/)
* [Elmish](https://fable-elmish.github.io/elmish/)

*Currently, the template does not include any Azure / other Cloud integration except Docker deployment. Refer to the [SAFE-Bookstore](https://github.com/SAFE-Stack/SAFE-BookStore) repository for an example that uses Azure Table Storage. We're working on improving this to include standard Azure dependencies and other deployment mechanisms.*

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

### Fable.Remoting
*Configures the SAFE app to use [Fable.Remoting](https://github.com/Zaid-Ajaj/Fable.Remoting) to the template. See [here](feature-clientserver.md) for an overview of Fable Remoting and the alternatives for sharing data between client and server.*

Usage: `dotnet new SAFE --Remoting`

### Docker
*Configures the SAFE app to use a [FAKE](https://fake.build/) target that bundles and build a Docker image.*

Usage: `dotnet new SAFE --Docker`

### NPM
*Configures the SAFE app to use NPM instead of default Yarn for JS package management.*

Usage: `dotnet new SAFE --NPM`

## Examples
* Create a SAFE application using all defaults: `dotnet new SAFE -lang F#`
* Create a SAFE application using giraffe with Fable Remoting: `dotnet new SAFE -lang F# --Server giraffe --Remoting`
* Create a SAFE application using Suave with Fulma: `dotnet new SAFE -lang F# --Server suave --Fulma landing`
