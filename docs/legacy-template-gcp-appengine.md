The SAFE template has the ability to deploy to [AppEngine](https://cloud.google.com/appengine/) on Google Cloud Platform, GCP, with minimum effort. The deployment relies on AppEngine's flex deployment, that is, it is deploying a docker container.

## Quickstart

First make sure you have `gcloud` cli tool [configured](legacy-template-google-cloud.md#setup-gcloud-cli-tool).
If you are already familiar with GCP and have `gcloud` in place, all you need to do to get started is:

```
dotnet new SAFE --deploy gcp-appengine
gcloud app create
dotnet fake build -t Deploy
gcloud app browse
```

## What is AppEngine

Google Cloud AppEngine is a fully managed serverless application platform. AppEngine supports an environment that is called [flexible environment](https://cloud.google.com/appengine/docs/flexible/). The flexible environment has native support for many different languages, and also an option of creating a custom environment. Even though .NET is supported, it is limited and focus on aspnet core, so the AppEngine support for SAFE is currently using the custom runtime for more flexibility.

Pricing for AppEngine depends on the size of and the number of machines you use.

## Deployment steps

Before you can deploy your AppEngine app you need to create a [Google Cloud Platform account](legacy-template-google-cloud.md#creating-an-account), [set up the `gcloud` CLI tool](legacy-template-google-cloud.md#setup-gcloud-cli-tool) and [enable billing](legacy-template-google-cloud.md#enable-billing).

### Custom FAKE build tasks

* **Bundle** - Runs after the standard Build step. It combines the outputs of the Client and Server application into a single folder.
* **Deploy** - Invokes the `gcloud` command to deploy to AppEngine.

### How is deploy done?

The deploy to AppEngine is not to complicated. The deploy leverage the fact that the AppEngine flex environment allows for custom runtimes. What that mean is that you basically can deploy an app that is defined by a docker container. In its simplest form you just need to have a `Dockerfile` present that defines the container and that will be built during deploy, and that is exactly how the deploy in this template is configured. We have a `app.yaml` file, that is used to configure your AppEngine app, and there we define runtime and also environment variables for the app. The `Deploy` task in the FAKE script invokes `gcloud app deploy --quiet`, which will deploy the app to the active google cloud project using the definition in the `app.yaml` and `Dockerfile`.

### Viewing the app

When deploy is done the app will be publicly available, and all you need to do to access the app is to run `gcloud app browse`.

## Things to consider

The main focus for the AppEngine template is to get you started. Exactly how you want to do the deployment depends on what your deployment process look like. For a small and simple app what you get here might be enough, but for a more complex scenario you might want to build the docker container separately and publish that to a container registry before running the actual deploy command. That makes it possible to disconnect the actual deploy of the container from the build of the container.

The application also has health and liveliness checks disabled, and that is something you might want to enable again in `app.yaml` and also implement it in your app. The health and liveliness checks is a feature of AppEngine to make sure your app is running as expected. 