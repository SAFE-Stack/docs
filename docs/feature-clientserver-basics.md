## Sharing Types
Sharing your domain types and contracts between client and server is extremely simple. Thanks to Fable's excellent F# transpilation into Javascript, you can use all standard F# language features such as Records, Tuples and Discriminated Unions without worry. To share types across both your client and server project, first create a file in your repository called e.g `Shared.fs`.
![](img/client-server-01.png)

Then, create types in the file as needed e.g

```fsharp
type Customer = { Id : int; Name : string }
```

Reference this file to your server project. You can now reference those types in the server.

```xml
<Project Sdk="Microsoft.NET.Sdk">
    ...
    <ItemGroup>
        <Compile Include="../Shared/Shared.fs" />
    </ItemGroup>
    ...
</Project>
```

Finally, reference this file to your client project (as above). You can now reference those types in the client; Fable will automatically convert your F# types into Javascript in the background.

## Sharing Behaviour
You can also share code using the same mechanism as sharing types. This is extremely useful for e.g shared validation or business logic that needs to occur on both client and server.

Fable will translate your functions into native Javascript, and will even translate many calls to the .NET base class library into corresponding Javascript! This allows you to compile your domain model and domain logic to many many different targets including:

* ASP.NET Core (via Saturn)
* Azure Functions
* Javascript that runs in the browser
* Javascript that runs on mobile devices with [React Native](https://facebook.github.io/react-native/).
* Raspberry Pi (via .NET Core)

You can read more about this [on the Fable website](http://fable.io/docs/compatibility.html).
