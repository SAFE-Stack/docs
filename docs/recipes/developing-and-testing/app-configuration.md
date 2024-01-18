# How do I add custom configuration?
There are many ways to supply configuration settings e.g. connection strings to your application, and the [official ASP .NET documentation](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-8.0) explains in great detail the various options you have available.

## Configuration of the Server
In this recipe, we show how to add configuration using an `appsettings.json` configuration file.

> Never store secrets in plain text files such as `appsettings.json` - our recommendation is to use it for non-secret configuration data that is shared across your development team; see [here](https://learn.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-8.0&tabs=windows) for more guidance.

1. In the Server folder, add a file `appsettings.json`. *It does not need to be added to the  project file.*
2. Add the following content to it:
```json
{
    "MyKey": "My appsettings.json Value"
}
```
3. In `Server.fs`, ensure that your API builder functions take in an `HTTPContext` as an argument and change the construction of your Fable Remoting endpoint to supply one:
```diff
++open Microsoft.AspNetCore.Http

--let todosApi = {
++let todosApi (context: HttpContext) = {

...

--    |> Remoting.fromValue todosApi
++    |> Remoting.fromContext todosApi
```
4. Use the `context` to get a handle to the `IConfiguration` object, which allows you to access settings regardless of what infrastructure you are using to store them e.g. `appsettings.json`, environment variables etc.
```diff
++open Giraffe
++open Microsoft.Extensions.Configuration

let todosApi (context: HttpContext) =
++    let cfg = context.GetService<IConfiguration>()
++    let value = cfg["MyKey"] // "My appsettings.json Value"
```
> Note that the `todosApi` function will be called on every single ASP .NET request. It is  safe to "capture" the `cfg` value and use it across multiple API methods.

## Working with User Secrets
User Secrets are an alternative way of storing secrets which, although still stored in plain text files, are not stored in your repository folder and therefore less at risk to accidentally committing into source control. However, Saturn currently disables User Secrets as part of its startup routine, and you must manually turn them back on:

```diff
++type DummyType() = class end

let app = application {
++    host_config (fun hostBuilder ->
++        hostBuilder.ConfigureAppConfiguration(fun _ configBuilder ->
++            configBuilder.AddUserSecrets<DummyType>()
++            |> ignore
++        )
++    )
}
```

You can then access the `IConfiguration` as before, and user secrets values will be accessible.

## Configuration of the client
Configuration of the client can be done in many ways, but generally a simple strategy is to have an API endpoint which is called on startup that provides any settings required by the client.