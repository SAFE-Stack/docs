# How do I use Giraffe instead of Saturn?

If you would rather have a more traditional ASP .NET experience than [Saturn](https://saturnframework.org/explanations/overview.html) provides whilst still working in a functional way, you can easily remove it from the SAFE templates and interact with [Giraffe](https://github.com/giraffe-fsharp/Giraffe/blob/master/DOCUMENTATION.md#giraffe-documentation) directly.

## Bootstrapping the Application

### 1. Open libraries

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

### 2. Replace application

We need to replace the Server's `application` computation expression with functions which set up the default host, the configure the application and register services.

Remove this

```fsharp
let app =
    application {
        // ...setup functions
    }

run app
```

and replace it with this

```fsharp
let configureApp (app : IApplicationBuilder) =
    app
        .UseStaticFiles()
        .UseGiraffe webApp
   
let configureServices (services : IServiceCollection) =
    services
        .AddGiraffe() |> ignore


Host.CreateDefaultBuilder()
    .ConfigureWebHostDefaults(
        fun webHostBuilder ->
            webHostBuilder
                .Configure(configureApp)
                .ConfigureServices(configureServices)
                .UseUrls([|"http://0.0.0.0:8085"|])
                .UseWebRoot("public")
                |> ignore)
    .Build()
    .Run()
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

As with any Server setup however, there are many more optional parameters that you may wish to configure, such as [caching](https://github.com/SaturnFramework/Saturn/blob/4b11a4685e5e27f1df6d6195c1d3e965adfb0ec1/src/Saturn/Application.fs#L260), [response compression](https://github.com/SaturnFramework/Saturn/blob/4b11a4685e5e27f1df6d6195c1d3e965adfb0ec1/src/Saturn/Application.fs#L270) and [serialisation](https://github.com/SaturnFramework/Saturn/blob/4b11a4685e5e27f1df6d6195c1d3e965adfb0ec1/src/Saturn/Application.fs#L580) options amongst many others.

See the [Giraffe](https://github.com/giraffe-fsharp/Giraffe/blob/master/DOCUMENTATION.md#basics) and ASP .NET Core [host builder](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/web-host?view=aspnetcore-3.1), [application builder](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1) and [service collection](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1) docs for more information on this.