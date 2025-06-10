# How Do I Add Support for Fable Remoting?
[Fable Remoting](https://zaid-ajaj.github.io/Fable.Remoting/) is a type-safe RPC communication layer for SAFE apps. It uses HTTP behind the scenes, but allows you to program against protocols that exist across the application without needing to think about the HTTP plumbing, and is a great fit for the majority of SAFE applications.

> Note that the standard template uses Fable Remoting. **This recipe only applies to the minimal template**.

#### 1. Install NuGet Packages
Add [Fable.Remoting.Giraffe](https://www.nuget.org/packages/Fable.Remoting.Giraffe/) to the Server and [Fable.Remoting.Client](https://www.nuget.org/packages/Fable.Remoting.Client/) to the Client.

> See [How Do I Add a NuGet Package to the Server](../package-management/add-nuget-package-to-server.md)
> and [How Do I Add a NuGet Package to the Client](../../recipes/package-management/add-nuget-package-to-client.md).

#### 2. Create the API protocol
You now need to create the protocol, or contract, of the API we’ll be creating. Insert the following below the `Route` module in `Shared.fs`:
```fsharp
type IMyApi =
    { hello : unit -> Async<string> }
```

#### 3. Create the routing function
We need to provide a basic routing function in order to ensure client and server communicate on the
same endpoint. Find the `Route` module in `src/Shared/Shared.fs` and replace it with the following:

```fsharp
module Route =
    let builder typeName methodName =
        sprintf "/api/%s/%s" typeName methodName
```

#### 4. Create the protocol implementation
We now need to provide an implementation of the protocol on the server. Open `src/Server/Server.fs` and insert the following right after the `open` statements:

```fsharp
let myApi =
    { hello = fun () -> async { return "Hello from SAFE!" } }
```

#### 5. Hook into ASP.NET
We now need to "adapt" Fable Remoting into the ASP.NET pipeline by converting it into a Giraffe HTTP Handler. Don't worry - this is not hard. Find `webApp` in `Server.fs` and replace it with the following:

```fsharp
open Fable.Remoting.Server
open Fable.Remoting.Giraffe

let webApp =
    Remoting.createApi()
    |> Remoting.withRouteBuilder Route.builder // use the routing function from step 3
    |> Remoting.fromValue myApi // use the myApi implementation from step 4
    |> Remoting.buildHttpHandler // adapt it to Giraffe's HTTP Handler
```

#### 6. Create the Client proxy
We now need a corresponding client proxy in order to be able to connect to the server. Open `src/Client/Client.fs` and insert the following right after the `Msg` type:
```fsharp
open Fable.Remoting.Client

let myApi =
    Remoting.createApi()
    |> Remoting.withRouteBuilder Route.builder
    |> Remoting.buildProxy<IMyApi>
```

> Note: We have made your life easier by creating Metapackages which abstract some of this boilerplate. You can also create the client proxy like so:
> ```fsharp
> open SAFE
>
> let myApi = Api.makeProxy<IMyApi> ()
> ```

#### 7. Make calls to the Server
Replace the following two lines in the `init` function in `Client.fs`:

```fsharp
let getHello() = Fetch.get<unit, string> Route.hello
let cmd = Cmd.OfPromise.perform getHello () GotHello
```

with this:

```fsharp
let cmd = Cmd.OfAsync.perform myApi.hello () GotHello
```

## Done!
At this point, the app should work just as it did before. Now, expanding the API and adding a new endpoint is as easy as adding a new field to the API protocol we defined in `Shared.fs`, editing the `myApi` record in `Server.fs` with the implementation, and finally making calls from the proxy.
