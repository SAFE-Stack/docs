One of SAFE's best features is the ability to share **data**, **types** and **code** across client and server. This page illustrates the different ways this can be achieved.

## Sharing Types
Sharing your domain types and contracts between client and server is extremely simple. Thanks to Fable's excellent F# transpilation into Javascript, you can use all standard F# language features such as Records, Tuples and Discriminated Unions without worry. To share types across both your client and server project, first create a file in your repository called e.g `Shared.fs`.
![](img\client-server-01.png)

Then, create types in the file as needed e.g

```fsharp
type Customer = { Name : string }
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

Finally, reference this file to your client project (as above). You can now reference those types in the client; types will be compiled into Javascript types and seamlessly translated for you.

## Sharing Code
You can also share code using the same mechanism. This is extremely useful for e.g shared validation or business logic that needs to occur on both client and server.

Fable will translate your functions into native Javascript, and will even translate many calls to the .NET BCL into javascript! You can read more about this [on the Fable website](http://fable.io/docs/compatibility.html).

## Sharing Data
Sharing data can be achieved in two main ways in SAFE: through **raw HTTP methods**, such as GET and POST etc., or via the **Fable.Remoting** package.

### Sharing using raw HTTP
Sharing data using raw HTTP methods is very simple. Start by creating a function in your server that returns some data:

```fsharp
let loadCustomersFromDb() =
    [ { Name = "Joe Bloggs" } ]
```
Next, create a method which returns the data as JSON within Giraffe's HTTP context.
```fsharp
/// Returns the results of loadCustomersFromDb as JSON.
let getCustomers next ctx =
    json (loadCustomersFromDb()) next ctx
```
You can opt to combine both of the above functions into one, depending on your preferences, but it's often good practice to separate your data access from serving data in HTTP endpoints.

Also note the `next` and `ctx` arguments. These are used by Giraffe as part of its HTTP pipeline and are required by the `json` function.

Now expose the api method using Saturn's `scope` construct and add the scope to your overall application scope:
```fsharp
let myApis = scope {
    get "api/customers/" getCustomers
}
```

For simple endpoints you may elect to embed the API call directly in the scope (and use partial application to omit the `next` and `ctx` arguments):
```fsharp
let myApis = scope {
    get "/customers/" (json (loadCustomersFromDb()))
}
```

Finally, call the endpoint from your client application.
```fsharp
promise {    
    let! customers = Fetch.fetchAs<Customer array> (sprintf "api/customers") []
    // do more with customers here...
}
```

Note the use of the `promise { }` computation expression. This functions like `async { }` blocks that you already know, whilst the `fetchAs` function automatically deserializes the JSON back into a `Customer` array.

#### Turning on Fable's JSON Converter
By default, serialization between Fable and Giraffe **is not compatible**. In order to fix this, you must replace the JSON converter in Giraffe with Fable's own `IJsonSerializer`.

If you are using the dotnet SAFE Template, this will automatically be done for you - see the `config` function in `Server.fs`.

### Sharing data using Fable.Remoting

### When should I use raw HTTP vs Fable Remoting?
