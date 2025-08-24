## Sharing Types
Sharing your domain types and contracts between client and server is extremely simple. Thanks to Fable's excellent F# transpilation into JavaScript, you can use all standard F# language features such as Records, Tuples and Discriminated Unions without worry. To share types across both your client and server project, first create a project in your repository called e.g `Shared.fsproj`. This project will contain any assets that should be shared across the client and server e.g. types and functions.

Then, create files with your types in the project as needed e.g

```fsharp
type Customer = { Id : int; Name : string }
```

Reference this project from your server project. You can now reference those types on the server.

```xml
<Project Sdk="Microsoft.NET.Sdk">
    ...
    <ItemGroup>
        <ProjectReference Include="..\Shared\Shared.fsproj" />
    </ItemGroup>
    ...
</Project>
```

Finally, reference this project in your client project (as above). You can now reference those types on the client; Fable will automatically convert your F# types into JavaScript in the background.

## Sharing Behaviour
You can also share behaviour using the same mechanism as for sharing types. This is extremely useful for e.g shared validation or business logic that needs to occur on both client and server.

Fable will translate your functions into native JavaScript, and will even translate many calls to the .NET base class library into corresponding JavaScript! This allows you to compile your domain model and domain logic to many many different targets including:

* ASP.NET Core (via Saturn)
* Azure Functions
* JavaScript that runs in the browser
* JavaScript that runs on mobile devices with [React Native](https://facebook.github.io/react-native/).
* Raspberry Pi (via .NET Core)

You can read more about this [on the Fable website](http://fable.io/docs/compatibility.html).

## Conditional sharing
When sharing assets between client and server, you may wish to have different implementations for the "client" and "server" sides. For example, if the client-side version of a function should call an NPM package but the server-side version should use a NuGet package. This is a more advanced scenario where you may require different implementations for the JS and .NET versions of code. In such situations, you can use `#IF` directives to conditionally compile code for either platform - see the [Fable](https://fable.io/) website for more information.
