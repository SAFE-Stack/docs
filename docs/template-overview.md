The [SAFE Template](https://github.com/SAFE-Stack/SAFE-template) is a [dotnet CLI template](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-new?tabs=netcore2x) for SAFE Stack projects, designed to get you up and running as quickly as possible, with flexible options to suit your application.

The template gets you up and running with the most common elements of the stack:

* [Saturn](https://saturnframework.github.io/docs/) or [Giraffe](https://github.com/giraffe-fsharp/Giraffe)
* [Fable](http://fable.io/) for client-side F#
* [Elmish](https://elmish.github.io/) for web UI
* [Fulma](https://fulma.github.io/Fulma/) for consistent web styling
* [Docker](template-docker.md), [Azure App Service](template-appservice.md), [Google Cloud AppEngine](template-gcp-appengine.md), [Google Cloud Kubernetes Engine](template-gcp-kubernetes.md) or [Heroku](template-heroku.md) deployment models for hosting.

## Using the template

Refer to the [Quick Start guide](quickstart.md#create-your-first-safe-app) to see basic guidance on how to install and use the template.

## Examples

* Create a SAFE application using all defaults:

`dotnet new SAFE`

* Create a SAFE application using Giraffe with Fable Remoting:

`dotnet new SAFE --server giraffe --communication remoting`

* Create a SAFE application using Giraffe with Fulma ['Landing'](https://bulmatemplates.github.io/bulma-templates/templates/landing.html):

`dotnet new SAFE --server giraffe --layout fulma-landing`

## Template options

The template provides the ability to customise the created application. You can see template version and available options by running  `dotnet new SAFE --help`.

### Server

*Configures the SAFE app to use one of two different back-end hosting platforms.*

Usage: `dotnet new SAFE --server <server>`

Where `<server>` is one of:

* `saturn`: Creates a SAFE app running on Saturn on top of Giraffe **(default)**.
* `giraffe`: Creates a SAFE app running on Giraffe only.

### Layout

*Configures the SAFE app to apply a CSS Framework to the UI template. Currently supports just [Fulma](https://fulma.github.io/Fulma) bindings*

Usage: `dotnet new SAFE --layout <layout>`

Where `<layout>` is one of:

* `none`: don't add any CSS framework.
* `fulma-basic`: adds Fulma basic template **(default)**.
* `fulma-admin`: adds Fulma with the ['Admin'](https://bulmatemplates.github.io/bulma-templates/templates/admin.html) Bulma template.
* `fulma-cover`: adds Fulma with the ['Cover'](https://bulmatemplates.github.io/bulma-templates/templates/cover.html) Bulma template.
* `fulma-hero`: adds Fulma with the ['Hero'](https://bulmatemplates.github.io/bulma-templates/templates/hero.html) Bulma template.
* `fulma-landing`: adds Fulma with the ['Landing'](https://bulmatemplates.github.io/bulma-templates/templates/landing.html) Bulma template.
* `fulma-login`: adds Fulma with the ['Login'](https://bulmatemplates.github.io/bulma-templates/templates/login.html) Bulma template.

### Communication

*Configures the SAFE app to use one of available means of communication between client and server. Currently supports [Fable.Remoting](https://github.com/Zaid-Ajaj/Fable.Remoting) and [Elmish.Bridge](https://github.com/Nhowka/Elmish.Bridge). If this argument is not supplied, client / server communication will be handled through the standard routing and serialization mechanism of the server. See [here](feature-clientserver.md) for an overview of Fable.Remoting, Elmish.Bridge and the alternatives for sharing data between client and server.*

Usage: `dotnet new SAFE --communication <communication model>`

Where `<communication model>` is one of:

* `none`: don't add any additional libraries for client-server communication **(default)**
* `remoting`: add [Fable.Remoting](https://github.com/Zaid-Ajaj/Fable.Remoting) to the template.
* `bridge`: add [Elmish.Bridge](https://github.com/Nhowka/Elmish.Bridge) to the template.

### Pattern

*Configures Client app to use one of available patterns. Currently supports only either default Elmish architecture with Commands or simple (no Commands) Elmish Architecture in conjunction with [Elmish.Streams](https://elmish-streams.readthedocs.io/) library for reactive programming*

Usage: `dotnet new SAFE --pattern <pattern model>`

Where `<pattern model>` is one of:

* `default`: use standard Elmish architecture with Commands **(default)**
* `streams`: use simple Elmish architecture without Commands + [Elmish.Streams](https://elmish-streams.readthedocs.io/) for reactive pattern

### Deploy

*Optionally configures the SAFE app to elements needed for deploying to one of two different hosting models. If this argument is not supplied, no explicit support for any hosting model will be provided.*

Usage: `dotnet new SAFE --deploy <hosting model>`

Where `<hosting model>` is one of:

* `none`: don't add FAKE targets to deploy **(default)**.
* `docker`: Adds [FAKE](https://fake.build/) targets that bundles and build a Docker image. See [here](template-docker.md) for more details about Docker deployment.
* `azure`: Adds [FAKE](https://fake.build/) targets and an [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) (ARM) template that allows deployment to the [Azure App Service](https://azure.microsoft.com/en-us/services/app-service/) plus an [Azure Application Insights](https://azure.microsoft.com/en-us/services/application-insights/) instance. See [here](template-appservice.md) for more details about Azure deployment.
* `gcp-appengine`: Adds [FAKE](https://fake.build/) targets to deploy to [Google Cloud AppEngine](https://cloud.google.com/appengine/).
* `gcp-kubernetes`: Adds [FAKE](https://fake.build/) targets to deploy to [Google Cloud Kubernetes Engine](https://cloud.google.com/kubernetes-engine/).
* `iis`: Adds [FAKE](https://fake.build/) targets and client paths normalization to easily publish the application to [IIS](https://www.iis.net/).
* `heroku`: Adds [FAKE](https://fake.build/) targets to deploy to [Heroku](https://heroku.com/).

### JS Deps

*Configures the SAFE app to use either Yarn or NPM for JS package management.*

Usage: `dotnet new SAFE --js-deps <package manager>`

Where `<package manager>` is one of:

* `yarn`: uses [Yarn](https://yarnpkg.com/) for JS package management  **(default)**.
* `npm`: uses [NPM](https://www.npmjs.com/) for JS package management. If you use NPM, you'll also need [NPX](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b) to run scripts.
