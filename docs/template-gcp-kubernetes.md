The SAFE template has the ability to deploy to [Kubernetes Engine](https://cloud.google.com/kubernetes-engine/) on Google Cloud Platform, GCP, with minimum effort. The focus of the template is to set up the basic building blocks like creating the container, publishing the container and also create a simple deployment on kubernetes that exposes a kubernetes service. You probably want to, and should, add things like a proper hostname and https support. That can be done in multiple ways and is more of a kubernetes specific task than a SAFE task.

## Quickstart

If you are familiar with GCP and already have `gcloud` and `kubectl` configured and an existing kubernetes cluster all you need to do to get started is running these commands:

```
dotnet new SAFE --deploy gcp-kubernetes
fake build -t Deploy
```

The quickstart assumes you have a default zone configured, and it will deploy to a cluster named `safe-cluster`. To change the cluster add `-e SAFE_CLUSTER=<kubernetes cluster>` to the `fake` command. To get the exposed IP you need to run the following command:

```
kubectl get service safe-template
```

## What is Kubernetes Engine

[Kubernetes Engine](https://cloud.google.com/kubernetes-engine/) is a managed, production-ready environment for deploying containerized applications on Google Cloud Platform.

Compared to AppEngine you will get faster deploy, but it also comes at a cost of higher operational cost. Total cost might be lower though depending on how many apps you run and how large your cluster is. One cluster can often host multiple apps, while each AppEngine app requires a single machine so with many apps AppEngine might be more expensive.

Covering Kubernetes and Kubernetes Engine in greater detail is too large of a topic in this context, and there are a lot of tutorials on those topics available.

## Deployment steps

Before you can deploy your Kubernetes Engine app you need to create a [Google Cloud Platform account](template-google-cloud.md#creating-an-account), [set up the `gcloud` CLI tool](template-google-cloud.md#setup-gcloud-cli-tool) and [enable billing](template-google-cloud.md#enable-billing). The kubernetes deploy also rely on Google Cloud Container Registry, and you also need to authenticate with the registry. How that is done is described here: [https://cloud.google.com/container-registry/docs/advanced-authentication](https://cloud.google.com/container-registry/docs/advanced-authentication).

### Creating your cluster and installing kubectl

To make some of the commands easier we first [set a default zone](https://cloud.google.com/compute/docs/gcloud-compute/#set_default_zone_and_region_in_your_local_client) that will be used by the `gcloud` command, both here and from the build script.

```
gcloud config set compute/zone europe-west1-b 
```

You can change the zone to a zone closer to where you live, [https://cloud.google.com/compute/docs/regions-zones/](https://cloud.google.com/compute/docs/regions-zones/).

Next up you need to create your kubernetes cluster. To create a cluster where the nodes are using of the free tier computing resources you can use this command:

```
gcloud container clusters create safe-cluster --num-nodes=3 -m f1-micro
```

To interact with the kubernetes cluster you also need to install `kubectl`. To get the correct version, that is, guaranteed to work with kubernetes engine, it is recommended to install it using `gcloud`: 

```
gcloud components install kubectl
```

With this configured and installed you should be good to go.

### Custom FAKE build tasks

* **Bundle** - Runs after the standard Build step. It combines the outputs of the Client and Server application into a single folder.
* **Docker** - Based on present the `Dockerfile` a docker container image is built and tagged using the current google cloud project, name of the app and git hash.
* **Publish** - Publish the docker container image from the `Docker` task that matches the current git hash.
* **ClusterAuth** - Authenticates against the Google Cloud Kubernetes Cluster
* **Deploy** - Invokes `kubectl` to either deploy and expose the app or update the docker image for the deployment.

### How is deploy done?

The actual deploy is the simplest possible deploy you can do on a kubernetes cluster. When the `Deploy` target runs it first checks if the deploy exists or not. If the deploy doesn't exist it will invoke the `kubectl run <name> --image=<image name> --port <port>` command. That oneline command will create a kubernetes [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/), a [service](https://kubernetes.io/docs/concepts/services-networking/service/), a [deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) and also exposing the service.

If the deployment already exists instead of deploying the container an update to the image of the pod will be made by running `kubectl set image ...`. 

The container image that is used is the one that has been published to [Google Cloud Container Registry](https://cloud.google.com/container-registry/) in the `Publish` step. This is just to make it easier to deploy when everything is on the same platform.

### Viewing the app

No hostname is created during the deploy, and no reverse proxy is configured either, so to view the app you need to get the exposed service external IP:

```
kubectl get service safe-template
```

## Gotchas

When you deploy a container to a kubernetes cluster using the container name and tag you might not get an updated version of the contaienr if you use the same tag. The easiest way to change the tag using the default template is to change the `getDockerTag` function in `build.fsx`. One common way to create the tag is to use the git sha, given that you use git. To do so you can change the `getDockerTag` to something like below:

```
let getDockerTag projectName =
    let gitHash = Information.getCurrentHash()
    let projectId = getGcloudProject()
    sprintf "gcr.io/%s/%s:%s" projectId projectName gitHash
```

to be sure you get a new docker tag you have to make a commit.

## Things to consider

As mentioned in the beginning, this template is meant to get you started. You will need to configure https, hostnames and other things most likely. There are too many options to consider there to include it in this template, and you might have already invested in some of those options.
