# How Do I Add Support for Fable Remoting?
There are several steps to migrating to [Fable Remoting](https://github.com/Zaid-Ajaj/Fable.Remoting). There is no reason to be intimidated, though. If you follow the instructions, it will all make sense in the end. However, If you go through the whole recipe and still have questions, I encourage you to take a look at the [Fable Remoting docs](https://zaid-ajaj.github.io/Fable.Remoting/).

> Note that the standard template uses Fable Remoting by default, and **this recipe only applies to the minimal template**.

#### 1. NuGet Packages
##### 1a. Server
Add [Fable.Remoting.Giraffe](https://www.nuget.org/packages/Fable.Remoting.Giraffe/) to the Server.
> See [How Do I Add a NuGet Package to the Server](../../package-management/add-nuget-package-to-server).
##### 1b. Client
Add [Fable.Remoting.Client](https://www.nuget.org/packages/Fable.Remoting.Client/) to the Client.
> See [How Do I Add a NuGet Package to the Client](../../package-management/add-nuget-package-to-client).

#### 2. Route Builder
Find the `Route` module in `src/Shared/Shared.fs` and replace it with the following:
```fsharp
module Route =
    let builder typeName methodName =
        sprintf "/api/%s/%s" typeName methodName
```
As you might have guessed, this function will be needed to build routes later on.

#### 3. API Interface
Insert the following just below the `Route` module in `Shared.fs`:
```fsharp
type IMyApi =
    { hello : unit -> Async<string> }
```
This is the type definition for the API we’ll be creating.

#### 4. The Server API
Open `src/Server/Server.fs` and insert the following right after the `open` statements:
```fsharp
let myApi =
    { hello = fun () -> async { return "Hello from SAFE!" } }
```
This will be used as the API by Fable Remoting, and it implements the interface we have created previously.

#### 5. WebApp
Find `webApp` in `Server.fs` and replace it with the following:
```fsharp
open Fable.Remoting.Server
open Fable.Remoting.Giraffe

let webApp =
    Remoting.createApi()
    |> Remoting.withRouteBuilder Route.builder
    |> Remoting.fromValue myApi
    |> Remoting.buildHttpHandler
```
This may seem complicated, but all it does at a high level is to use the `Route.builder` function and `myApi` record we’ve previously created to create a Remoting API that we can hit from the Client to retrieve results.

#### 6. The Client API
Now that we have the Remoting API set up in Server, we need a corresponding Client API in order to be able to hit the on in the Server. Open `src/Client/Client.fs` and insert the following right after the `Msg` type:
```fsharp
open Fable.Remoting.Client

let myApi =
    Remoting.createApi()
    |> Remoting.withRouteBuilder Route.builder
    |> Remoting.buildProxy<IMyApi>
```

#### 7. Call to the Server
*Observe* the following two lines in the `init` function in `Client.fs`.
```fsharp
let getHello() = Fetch.get<unit, string> Route.hello
let cmd = Cmd.OfPromise.perform getHello () GotHello
```
This is the way the Client in the minimal template get the “Hello…” string from the Server by default. We do not need these lines anymore. Replace them with the following:
```fsharp
let cmd = Cmd.OfAsync.perform myApi.hello () GotHello
```

## Done!
At this point, the app should work just as it did before. Now, expanding the API and adding a new endpoint is as easy as adding a new field to the API interface we have defined in `Shared.fs` and editing the `myApi` record in `Server.fs` to comply with the new interface. Then you can hit that endpoint from `Client.fs` using the `myApi` API we’ve created in step 6.