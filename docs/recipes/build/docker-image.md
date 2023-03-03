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

#### 2. Create the dockerfile

Create a `Dockerfile` with the following contents:

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:6.0 as build

# Install node
RUN curl -sL https://deb.nodesource.com/setup_16.x | bash
RUN apt-get update && apt-get install -y nodejs

WORKDIR /workspace
COPY . .
RUN dotnet tool restore

RUN dotnet run Bundle


FROM mcr.microsoft.com/dotnet/aspnet:6.0-alpine
COPY --from=build /workspace/deploy /app
WORKDIR /app
EXPOSE 5000
ENTRYPOINT [ "dotnet", "Server.dll" ]
```

This uses [multistage builds](https://docs.docker.com/develop/develop-images/multistage-build/) to keep the final image small.

##### Using the minimal template?

Replace the line

```dockerfile
RUN dotnet run Bundle
```

with

```dockerfile
RUN npm install
RUN dotnet fable src/Client --run webpack
RUN cd src/Server && dotnet publish -c release -o ../../deploy
```

#### 3. Building and running with docker locally

1. Build the image `docker build -t my-safe-app .`
2. Run the container `docker run -it -p 5000:80 my-safe-app`

> Because the build is done entirely in docker, Docker Hub [automated builds](https://docs.docker.com/docker-hub/builds/) can be setup to automatically build and push the docker image.

#### 4. Testing the server
Create a `docker-compose.server.test.yml` file with the following contents:

```yml
version: '3.4'
services:
    sut:
        build:
            target: build
            context: .
        working_dir: /workspace/tests/Server
        command: dotnet run
```
To run the tests execute the command `docker-compose -f docker-compose.server.test.yml up --build`

You can add server tests to the **minimal template** with the [testing the server](../developing-and-testing/testing-the-server.md) recipe.

> The template is not currently setup for automating the client tests in ci.

> Docker Hub can also run [automated tests](https://docs.docker.com/docker-hub/builds/automated-testing/) for you.

> Follow [the instructions to enable Autotest](https://docs.docker.com/docker-hub/builds/automated-testing/#enable-automated-tests-on-a-repository) on docker hub.

#### 5. Making the docker build faster

> Not recommended for most applications

If you often build with docker locally, you may wish to make the build faster by optimising the Dockerfile for caching. For example, it is not necessary to download all paket and npm dependencies on every build unless there have been changes to the dependencies.

Furthermore, the client and server can be built in separate build stages so that they are cached independently. Enable [Docker BuildKit](https://docs.docker.com/develop/develop-images/build_enhancements/) to build them concurrently.

This comes at the expense of making the dockerfile more complex; if any changes are made to the build such as adding new projects or migrating package managers, the dockerfile must be updated accordingly.

The following should be a good starting point but is not guarenteed to work.

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:6.0 as build

# Install node
RUN curl -sL https://deb.nodesource.com/setup_16.x | bash
RUN apt-get update && apt-get install -y nodejs

WORKDIR /workspace
COPY .config .config
RUN dotnet tool restore
COPY .paket .paket
COPY paket.dependencies paket.lock ./

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
RUN dotnet fable src/Client --run webpack


FROM mcr.microsoft.com/dotnet/aspnet:6.0-alpine
COPY --from=server-build /workspace/deploy /app
COPY --from=client-build /workspace/deploy /app
WORKDIR /app
EXPOSE 5000
ENTRYPOINT [ "dotnet", "Server.dll" ]
```
