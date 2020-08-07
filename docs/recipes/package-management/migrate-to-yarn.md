#How do I migrate to Yarn from NPM?

[Yarn](https://yarnpkg.com/) is an alternative Javascript package manager created by Facebook. 

When it was first released it provided several features which were absent from NPM at the time such as [offline caching](https://yarnpkg.com/features/offline-cache) and [deterministic dependency resolution](https://classic.yarnpkg.com/blog/2016/11/24/lockfiles-for-all/). 

Today however, NPM has largely gained feature parity with Yarn, although they still do things slightly differently and therefore many people prefer one over the other.

The SAFE template uses NPM out of the box, but it is easy to migrate to Yarn if you wish.

## Switching to Yarn

####1. Install Yarn

You will need to [install Yarn](https://classic.yarnpkg.com/en/docs/install) on your machine. 

> Please note: Version 2 of Yarn is [not currently supported](https://github.com/SAFE-Stack/SAFE-template/issues/329).

####2. Migrate from NPM

Navigate to the **Client** directory and simply run the command:
```powershell
yarn
```

####3. Sync package versions

If you want to make sure that Yarn is using the same package versions that NPM had in its lock file, run

```powershell
yarn import
```

Once this process is complete you can remove the NPM package-lock.json as it is no longer needed.

For more details on migration see the [official docs](https://classic.yarnpkg.com/en/docs/migrating-from-npm/).

## Running the application

Now that you have switched to using Yarn, you will need to modify the way in which the client application is built and launched.

The way in which you do this depends on whether you have started with the minimal or full template, as the latter uses [FAKE](https://fake.build/) to manage its build process.

### Launching the application from the minimal template

####1. Start the Client

Navigate to the **Client** project directory and run the command 
```powershell
yarn run start
```

####2. Start the Server

Navigate to the **Server** project directory and run
```powershell
dotnet run
```

### Launching the application from the full template

####1. Update FAKE build script

Open the build.fsx file that is in the root of the solution directory and replace all instances of "npm" with "yarn" (both in strings _and_ in value names).

####2. Update install args

Replace
```fsharp
Target.create "InstallClient" (fun _ -> yarn "install" clientPath)
```
with
```fsharp
Target.create "InstallClient" (fun _ -> yarn "install --frozen-lockfile" clientPath)
```

####3. Launch the application

At the root of your solution you can now just run 
```powershell
dotnet fake build -t run
```
as usual to launch both the Client and Server at the same time.