# How do I build with docker?

Using [Docker](https://www.docker.com/) makes it possible to deploy your application as a docker container or release an image on docker hub. This recipe walks you through creating a `Dockerfile` and automating the build and test process with [Docker Hub](https://hub.docker.com/).

#### 1. Create a .dockerignore file

Create a `.dockerignore` file with the same contents as `.gitignore`

##### Linux
```bash
cp .gitignore .dockerignore
```
##### Windows
```bash
copy .gitignore .dockerignore
```

Now, add the following lines to the `.dockerignore` file:

```
.git
```

## 2. Create the dockerfile

Create a `Dockerfile` with the following contents:

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.1 as build

# Install node
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash
RUN apt-get update && apt-get install -y nodejs

WORKDIR /workspace
COPY . .
RUN dotnet tool restore

RUN dotnet fake build -t Bundle


FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-alpine
COPY --from=build /workspace/deploy /app
WORKDIR /app
EXPOSE 8085
ENTRYPOINT [ "dotnet", "Server.dll" ]
```

This uses [multistage builds](https://docs.docker.com/develop/develop-images/multistage-build/) to keep the final image small.

### Using the minimal template
If you created your SAFE app using the **minimal** option, a [FAKE script](../../template-fake.md) is not included by default so you need to bundle up the client and server separately.

Replace the line

```dockerfile
RUN dotnet fake build -t Bundle
```

with

```dockerfile
RUN npm run build
RUN cd src/Server && dotnet publish -c release -o ../../deploy
```

## 3. Building and running with docker locally

1. Build the image `docker build -t my-safe-app .`
2. Run the container `docker run -it -p 8085:8085 my-safe-app`

## 4. Automated builds

Because the build is done entirely in docker, Docker Hub [automated builds](https://docs.docker.com/docker-hub/builds/) can be setup to automatically build and push the docker image.

## 5. Automated tests

Docker Hub can also run [automated tests](https://docs.docker.com/docker-hub/builds/automated-testing/) for you.

### Testing the server

Create a `docker-compose.server.test.yml` file with the following contents:

```yml
sut:
  build:
    context: .
    target: build
  command: cd tests/Server && dotnet run
```

> If you added tests to the **minimal template** according to the [testing the server](../developing-and-testing/testing-the-server.md) recipe, change the command to `cd src/Server.Tests && dotnet run`

### Testing the client

<!-- Create a `docker-compose.client.test.yml` file with the following contents:

```yml
sut:
  build:
    context: .
    target: build
  command: dotnet fake build -t runtests
```

> If you added tests to the **minimal template** according to the [testing the client](../developing-and-testing/testing-the-client.md) recipe, change the command to `cd src/Client.Tests && dotnet run` -->

The template is not currently setup for automating the client tests.

### Enable Autotest

Follow [the instructions to enable Autotest](https://docs.docker.com/docker-hub/builds/automated-testing/#enable-automated-tests-on-a-repository) on docker hub.

## 6. Making the docker build faster

> Not recommended for most applications

If you often build with docker locally, you may wish to make the build faster by optimising the Dockerfile for caching. For example, it is not necessary to download all paket and npm dependencies on every build unless there have been changes to the dependencies.

Furthermore, the client and server can be built in separate build stages so that they are cached independently. Enable [Docker BuildKit](https://docs.docker.com/develop/develop-images/build_enhancements/) to build them concurrently.

This comes at the expense of making the dockerfile more complex; if any changes are made to the build such as adding new projects or migrating package managers, the dockerfile must be updated accordingly.

The following should be a good starting point but is not guarenteed to work.

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.1 as build

# Install node
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash
RUN apt-get update && apt-get install -y nodejs

WORKDIR /workspace
COPY .config .config
RUN dotnet tool restore
COPY .paket .paket
COPY paket.dependencies paket.lock ./
RUN dotnet paket restore


FROM build as server-build
COPY src/Shared src/Shared
COPY src/Server src/Server
RUN cd src/Server && dotnet publish -c release -o ../../deploy


FROM build as client-build
COPY package.json package-lock.json ./
RUN npm install
COPY webpack.config.js ./
COPY src/Shared src/Shared
COPY src/Client src/Client
RUN npm run build


FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-alpine
COPY --from=server-build /workspace/deploy /app
COPY --from=client-build /workspace/deploy /app
WORKDIR /app
EXPOSE 8085
ENTRYPOINT [ "dotnet", "Server.dll" ]
```