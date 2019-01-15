In order to deploy to Google Cloud Platform, GCP, you first need to create a google cloud project.

## Creating an account

Creating an account is simple and you get some free credits when you sign up try GCP out. Go to https://cloud.google.com/ to sign up for an account. 

## Creating a project

A `project` on GCP is used to organize resources that belongs together one way or another. Creating a project is as simple as just clicking a button an write down the name of the project. If your `project name` is unique across GCP it will also be your `project id`, but if there are more than one project named the same you need to figure out your `project id`. You find the `project id` in the [Home dashboard](https://console.cloud.google.com/home/dashboard) in the GCP console. 

## Setup gcloud CLI tool

The command line interface for GCP is what most people use to interact with the platform instead of clicking in the UI. To get started with the tool you need to install it following the instructions here: [https://cloud.google.com/sdk/install](https://cloud.google.com/sdk/install).

When you have installed `gcloud` you need to authenticate and also set the default project to work with:

* Authenticate: `gcloud auth login`
* Set project: `gcloud config set project PROJECT_ID`

## Try it for free!

Google cloud give new users $300 to spend on cloud computing over a 12 month period from when you sign up. In addition to that you also get some free computing resources every month so you can test new things. However, you still need to enable billing and add a credit card to verify who you are. Read more about the [Google Cloud Platform Free Tier](https://cloud.google.com/free/).

As mentioned, you still need to add a credit card to your billing account and this is the way to go about it. Go to [Billing](https://console.cloud.google.com/billing) and create a billing account, you might have your free credits here already. When the billing account is created go back to the [Home dashboard](https://console.cloud.google.com/home/dashboard) and then select `Billing` again in the menu. The reason for this is that you first created a billing account that can be used in many projects, but now you also need to link your project to that billing account. On the billing page just click `Link a billing account` and link your project to the newly created billing account.
