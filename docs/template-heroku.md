In order to deploy to Heroku, you first need to create a Heroku account and download Heroku CLI.

## Creating an account

Creating an account is simple and free. Go to https://signup.heroku.com/ to sign up for an account.

## Setup Heroku CLI tool

The command line interface for Heroku is what the template uses to interact with the platform and create the projects. To get started with the tool you need to install it following the instructions here: [https://devcenter.heroku.com/articles/heroku-cli](https://devcenter.heroku.com/articles/heroku-cli).

When you have installed `heroku` you need to authenticate:

* Authenticate: `heroku login`

## Quickstart

After installing Heroku CLI and authenticating, the template makes the process of creating, configuring and deploying the project very straightforward:

```
dotnet new SAFE --deploy heroku
fake build -t Configure <optional app:create arguments>
fake build -t Deploy
```

After these steps if everything went right, the project should open on the browser.

### How is deploy done?

When you are ready to deploy, you need to add your changes to git staging area and create a commit. After you push your changes, Heroku will compile the project using the **Bundle** target, creating a [Procfile](https://devcenter.heroku.com/articles/procfile) that tells the instance what command it will need to run.

### Custom FAKE build tasks

* **Configure** - Needs to be run only once when creating the project. Makes the following configurations:
    1. Creates the project using the `heroku apps:create` command. If no argument is supplied, heroku generates a random name for the project, otherwise it tries to use the supplied name.
    1. Initializes the current folder as a Git repository and commits everything.
    1. Links the created repository to the project's remote git.
    1. Configures the buildpack that compiles and run the project on heroku's server.
* **Deploy** - Serves as an shortcut for `git push heroku master`. Uploads the current commit to the server so it can be deployed.
