One of the most powerful features of SAFE is the ability to seamlessly share **data**, **types** and **code** across client and server.

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

Finally, reference this file to your client project (as above). You can now reference those types in the client; types will be compiled into Javascript types and seamlessly translated for you.

## Sharing Code

You can also share code using the same mechanism. This is extremely useful for e.g shared validation or business logic that needs to occur on both client and server.

Fable will translate your functions into native Javascript, and will even translate many calls to the .NET base class library into corresponding Javascript! This allows you to compile your domain model and domain logic to many many different targets including:

* ASP.NET Core (via Saturn)
* Azure Functions
* Javascript that runs in the browser
* Javascript that runs on mobile devices with [React Native](https://facebook.github.io/react-native/).
* Raspberry Pi (via .NET Core)

You can read more about this [on the Fable website](http://fable.io/docs/compatibility.html).

## Sharing Data
Sharing data can be achieved in two main ways in SAFE: through the [Saturn](https://saturnframework.github.io/docs/) API directly, or via the [Fable.Remoting](https://github.com/Zaid-Ajaj/Fable.Remoting) library.

### Sharing data with Saturn
Sharing data using Saturn is very simple. Start by creating a function in your server that returns some data:

```fsharp
let loadCustomersFromDb() =
    [ { Id = 1; Name = "Joe Bloggs" } ]
```
Next, create a method which returns the data as JSON within Giraffe's HTTP context.

```fsharp
/// Returns the results of loadCustomersFromDb as JSON.
let getCustomers next ctx =
    json (loadCustomersFromDb()) next ctx
```

You can opt to combine both of the above functions into one, depending on your preferences, but it's often good practice to separate your data access from serving data in HTTP endpoints.

Also note the `next` and `ctx` arguments. These are used by Giraffe as part of its [HTTP pipeline](https://github.com/giraffe-fsharp/Giraffe/blob/master/DOCUMENTATION.md#fundamentals) and are required by the `json` function.

Now expose the api method using [Saturn's](https://saturnframework.github.io/docs/api/scope/) `scope` construct and add the scope to your overall application scope:
```fsharp
let myApis = scope {
    get "/api/customers/" getCustomers
}
```

For simple endpoints you may elect to embed the API call directly in the scope (and use partial application to omit the `next` and `ctx` arguments):
```fsharp
let myApis = scope {
    get "/api/customers/" (json (loadCustomersFromDb()))
}
```

Finally, call the endpoint from your client application.
```fsharp
promise {    
    let! customers = Fetch.fetchAs<Customer list> (sprintf "api/customers") []
    // do more with customers here...
}
```

Note the use of the `promise { }` computation expression. This behaves similarly to `async { }` blocks that you might already know, whilst the `fetchAs` function automatically deserializes the JSON back into a `Customer` array.

#### Turning on Fable's JSON Converter
By default, serialization between Fable and Giraffe **is not compatible**. In order to fix this, you must replace the JSON converter in Giraffe with Fable's own `IJsonSerializer`.

If you are using the [SAFE Template](safe-template), this will automatically be done for you - see the [`config`](https://github.com/SAFE-Stack/SAFE-template/blob/master/Content/src/Server/ServerSaturn.fs#L40L44) function in `Server.fs`.

### Data communication using Fable.Remoting
Alongside raw HTTP, you can also use [Fable.Remoting](https://github.com/Zaid-Ajaj/Fable.Remoting), *remoting* for short,  which provides an RPC-style mechanism for calling server endpoints. Remoting lets you define you client-server interactions as a record type, this type is commonly referred to as the *protocol* or *contract* that is shared between the server and the client. 

Each field of the record is either of type `Async<T>` or a function that returns `Async<T>`, for example:
```fsharp
type ICustomerApi = {
    getCustomers : unit -> Async<Customer list>
    findCustomerByName : string -> Async<Customer option>
}
```
The supported types used within the protocol can be any F# type: primitive values (int, string, DateTime, etc.), records, options, discrimincated unions, choice, result, maps, lists, arrays, tuples or any combination of them. 

On the server you would implement the protocol:
```fs
let getCustomers() = 
    async {
        return [
            { Id = 1; Name = "John Doe" }
            { Id = 2; Name = "Jane Smith" }
        ]
    }

let findCustomerByName (name: string) = 
    async {
        let! allCustomers = getCustomers()
        return allCustomers |> List.tryFind (fun c -> c.Name = name)
    }


let customerApi : ICustomerApi = {
    getCustomers = getCustomers
    findCustomerByName = findCustomerByName
} 
```
After [exposing a HttpHandler](https://zaid-ajaj.github.io/Fable.Remoting/src/saturn.html) from `customerApi` you can start calling the API from the client. 

```fsharp
module Server = 
    let api : ICustomerApi = 
        Remoting.createApi()
        |> Remoting.buildProxy<ICustomerApi>

async {
    let! customers = server.getCustomers()
    for customer in customers do
        printfn "#%d => %s" customer.Id customer.Name
}
```

Notice here, there is no need to create routes, JSON serialization, worry about HTTP verbs, or even involve yourself with the Giraffe pipeline. If you open your browser network tab, you can inspect what remoting is doing behind the scenes with [raw http communication](https://zaid-ajaj.github.io/Fable.Remoting/src/raw-http.html). 

## Sharing State
With the addition of the [Elmish.Bridge](feature-elmish-bridge.md) library, it's now possible to maintain a stateful server and send notifications to clients to maintain state.

## Which technology should I use?
Fable Remoting provides an excellent way to quickly get up and running with the SAFE stack. You can rapidly create contracts and have guaranteed type-safety between both client and server. Considor using remoting for rapid prototyping, since JSON serialization and Http routing is handled by the library, you only think of your client-server code in terms of types and stateless functions. If you need full control over the HTTP channel for returning specific status codes, using custom HTTP verbs or working with headers, then remoting is probably not for you. 

Alternatively, the raw HTTP model provided by Saturn with `scope { }` requires you to construct routes manually and does not guarantee that the client and endpoint have the same contract (you have to specify the same type on both sides yourself). However, Saturn gives you total control over the routing and verbs used. If you have a public API that is exposed not just to your own application but to third-parties, or you need more fine grained control over your routes and data, you should use this approach.

Lastly, Elmish Bridge provides an alternative way of modelling client/server communication. Unlike Fable Remoting, Elmish Bridge provides the same Elmish model on the server as well as the client, as well as the ability to send notificatiosn from the server back to the client via websockets.

Alternatively, consider using a combination of both Remoting and Saturn endpoints - Remoting for those that are used "internally" by your application, and Saturn for those exposed to external callers.

| | Fable.Remoting | Raw HTTP | Elmish.Bridge |
|-|:-:|:-:|:-:|
| Client / Server support | Very easy | Easy | Very Easy |
| State model | Stateless | Stateless | Stateful |
| "Open" API? | YES | Yes | No |
| HTTP Verbs? | POST and GET | Fully Configurable | None |
| Push messages? | No | No | Yes |
| Pipeline Control? | Limited | Full | Limited |