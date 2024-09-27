# How do I use Giraffe instead of Saturn?

[Saturn](https://saturnframework.org/explanations/overview.html) is a functional alternative to MVC and Razor which sits on top of [Giraffe](https://github.com/giraffe-fsharp/Giraffe/blob/master/DOCUMENTATION.md#giraffe-documentation). Giraffe itself is a functional wrapper around the ASP.NET Core web framework, making it easier to work with when using F#.

Since Saturn is built on top of Giraffe, migrating to using "raw" Giraffe is relatively simple to do.

## Bootstrapping the Application

#### 1. Open libraries

Navigate to the **Server** module in the **Server** project.

Remove
```fsharp
open Saturn
```
and replace it with
```fsharp
open Giraffe
open Microsoft.AspNetCore.Builder
open Microsoft.Extensions.DependencyInjection
open Microsoft.Extensions.Hosting
open Microsoft.AspNetCore.Hosting
```

#### 2. Replace application

In the same module, we need to replace the Server's `application` computation expression with some functions which set up the default host, configure the application and register services.

Remove this

```fsharp
let app =
    application {
        // ...setup functions
    }

[<EntryPoint>]
let main _ =
    run app
    0
```

and replace it with this

```fsharp
let configureApp (app : IApplicationBuilder) =
    app
        .UseFileServer()
        .UseGiraffe webApp

let configureServices (services : IServiceCollection) =
    services
        .AddGiraffe() |> ignore

[<EntryPoint>]
let main _ =
    Host.CreateDefaultBuilder()
        .ConfigureWebHostDefaults(
            fun webHostBuilder ->
                webHostBuilder
                    .Configure(configureApp)
                    .ConfigureServices(configureServices)
                    .UseWebRoot("public")
                    |> ignore)
        .Build()
        .Run()
    0
```

## Routing

If you are using the standard SAFE template, there is nothing more you need to do, as routing is taken care of by Fable Remoting.

If however you are using the minimal template, you will need to replace the Saturn router expression with the Giraffe equivalent.

Replace this
```fsharp
let webApp =
    router {
        get Route.hello (json "Hello from SAFE!")
    }
```

with this
```fsharp
let webApp = route Route.hello >=> json "Hello from SAFE!"
```

## Other setup

The steps shown here are the minimal necessary to get a SAFE app running using Giraffe.

As with any Server setup however, there are many more optional parameters that you may wish to configure, such as [caching](https://github.com/SaturnFramework/Saturn/blob/4b11a4685e5e27f1df6d6195c1d3e965adfb0ec1/src/Saturn/Application.fs#L260), [response compression](https://github.com/SaturnFramework/Saturn/blob/4b11a4685e5e27f1df6d6195c1d3e965adfb0ec1/src/Saturn/Application.fs#L270) and [serialisation](https://github.com/SaturnFramework/Saturn/blob/4b11a4685e5e27f1df6d6195c1d3e965adfb0ec1/src/Saturn/Application.fs#L580) options as seen in the default SAFE templates amongst many others.

See the [Giraffe](https://github.com/giraffe-fsharp/Giraffe/blob/master/DOCUMENTATION.md#basics) and ASP.NET Core [host builder](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/web-host?view=aspnetcore-3.1), [application builder](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1) and [service collection](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1) docs for more information on this.