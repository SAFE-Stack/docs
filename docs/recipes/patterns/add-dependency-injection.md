> This recipe is not a detailed discussion of the pros and cons of Dependency Injection (DI) compared to other patterns. It simply illustrates how to use it within a SAFE Stack application!

1. Create a class that you wish to inject with a dependency (in this example, we use the built-in `IConfiguration` type that is included in ASP .NET):

    ```fsharp
    open Microsoft.Extensions.Configuration

    type DatabaseRepository(config:IConfiguration) =
        member _.SaveDataToDb (text:string) =
            let connectionString = config["SqlConnectionString"]
            // Connect to SQL using the above connection string etc.
            Ok 1
    ```

    > Instead of functions or modules, DI in .NET and F# only works with classes.

2. Register your type with ASP .NET, typically in the start up / bootstrapper.
    ```diff
    ++ open Microsoft.Extensions.DependencyInjection

       application {
    ++     service_config (fun services -> services.AddSingleton<DatabaseRepository>())
       }
    ```

    > [This section](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-8.0#lifetime-and-registration-options) of the official ASP .NET Core article explain the distinction between different lifetime registrations, such as Singleton and Transient.

3. Ensure that your Fable Remoting API can access the `HttpContext` type by using the `fromContext` builder function.
    ```diff
    let webApp =
        Remoting.createApi ()
    --  |> Remoting.fromValue createFableRemotingApi
    ++  |> Remoting.fromContext createFableRemotingApi
    ```

4. Within your Fable Remoting API, use the supplied `context` to retrieve your dependency:

    ```diff
    ++ open Microsoft.AspNetCore.Http

       let createFableRemotingApi
    ++     (context:HttpContext) =
    ++     let dbRepository = context.GetService<DatabaseRepository>()
           // ...
           // Return the constructed API record value...
    ```

    > Giraffe provides the `GetService<'T>` extension to allow you to quickly retrieve a dependency from the `HttpContext`.

    This will instruct ASP .NET to get a handle to the `DatabaseRepository` object; ASP .NET will automatically supply the `IConfiguration` object to the constructor. Whether a new `DatabaseRepository` object is constructed on each call depends on the lifetime you have registered it with.

> You can have your types depend on other types that you create, as long as they are registering into ASP .NET Core's DI container using methods such as `AddSingleton` etc.

## Further Reading
* [Official documentation on DI in ASP .NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-8.0)
* [Archived example PR to update the SAFE Template Todo App to use DI](https://github.com/SAFE-Stack/SAFE-template/pull/466/files)