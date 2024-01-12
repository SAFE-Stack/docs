# How do I add Appliation configuration (appsettings.json)?

This recipe describe how to add application configuration to the server. It uses the regular dotnet middleware

## configure server project

### create appsettings.json

1. create the **appsettings.json** file und src/Server with following content

    ```json
    {
        "AppSettings" : {
            "Setting1": "Value1",
            "Setting2": "Value2"
        }
    }
    ```

1. update the **src/Server/Server.fsproj** file with the following entry 
    ```xml 
    <ItemGroup>
        <None Include="appsettings.json" CopyToPublishDirectory="Always" />
    </ItemGroup>
    ```

### add middleware in **Server.fs**

1. open modules

    ```fs
    open Microsoft.Extensions.DependencyInjection
    open Microsoft.Extensions.Configuration
    open Microsoft.AspNetCore.Http
    ```

1. change addTodo function of todosApi like that

    ```fs
    let todosApi (context: HttpContext) = {

        addTodo = fun todo -> async {
                    // get configuration from appSettings (automatically loaded when appsettings.json present)
                    let configuration = context.GetService<IConfiguration>()

                    let appSettings = configuration.GetSection("AppSettings")
                    let setting1 = appSettings["Setting1"]
                    printfn $"Setting1 in addTodo: {setting1}"
                    
                    return
                        match Storage.addTodo todo with
                        | Ok() -> todo
                        | Error e -> failwith e
                }
    }
    ```

1. add configureServices before application

    ```fs 
    let configureServices (services : IServiceCollection) =
        // in case you need an environment specific settings file
        // let settingsFile = "appsettings." + Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") + ".json"
        let settingsFile = "appsettings.json"

        let configBuilder = ConfigurationBuilder()
        let config = configBuilder.AddJsonFile(settingsFile).Build()

        // in case one wants to read configuration and use it while preparing services etc.

        // test loaded settings
        let s1 = config["AppSettings:Setting1"]
        printfn $"Setting1: {s1}"

        // load section and then get setting
        let appSettings = config.GetSection("AppSettings")
        let setting = appSettings["Setting1"]
        printfn $"Setting1: {setting}"

        services

    ```

1. change Remoting.fromValue in webApp function into Remoting.fromContext

    ```fs
    let webApp =
        Remoting.createApi ()
        |> Remoting.withRouteBuilder Route.builder
        |> Remoting.fromContext todosApi 
        |> Remoting.buildHttpHandler

    ```

1. add service_config in application

    ```fs 
        let app = application {
            use_router webApp
            service_config configureServices // only needed to prepare further services
            memory_cache
            use_static "public"
            use_gzip
        }
    ```

    