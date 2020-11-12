# How do I send and receive data?
This recipe shows how to create an endpoint on the server and hook up it up to the client. This recipe assumes that you have also followed [this](mvu-roundtrip.md) recipe and have an understanding of MVU messaging. This recipe only shows how to wire up the client and server.

## **I'm using the standard template** (Fable Remoting)
[Fable Remoting](https://zaid-ajaj.github.io/Fable.Remoting/) is a library which allows you to create client/server messaging without any need to think about HTTP verbs or serialization etc.

### In Shared
#### 1. Update contract
Add your new endpoint onto an existing API contract e.g. `ITodosApi`. Because Fable Remoting exposes your API through F# on client and server, you get type safety across both.

```fsharp
type ITodosApi =
    { getCustomer : int -> Async<Customer option> }
```

### On the server
#### 1. Write implementation
Create a function that implements the back-end service that you require. Use standard functions to read from databases or other external sources as required.
```fsharp
let loadCustomer customerId = async {
    return Some { Name = "My Customer" }
}
```

> Note the use of `async` here. Fable Remoting uses async workflows, and not tasks. You *can* write functions that use task, but will have to at some point map to async using `Async.AwaitTask`.

#### 2. Expose your function
Tie the function you've just written into the API implementation.
```fsharp
let todosApi =
    { ///...
      getCustomer = loadCustomer
    }
```

#### 3. Test the endpoint (optional)
Test out your endpoint using e.g. web browser / Postman / REST Client for VS Code etc. See [here](https://zaid-ajaj.github.io/Fable.Remoting/src/raw-http.html) for more details on the required format.

### On the client
#### 1. Call the endpoint
Create a new function `loadCustomer` that will call the endpoint.

```fsharp
let loadCustomer customerId =
    Cmd.OfAsync.perform todosApi.getCustomer customerId LoadedCustomer
```

> Note the final value supplied, `CustomerLoaded`. This is the `Msg` case that will be sent into the
> Elmish loop *once the call returns*, with the returned data. It should take in a value that
> matches the type returned by the Server e.g. `CustomerLoaded of Customer option`. See [here](mvu-roundtrip.md)
> for more information.

This can now be called from within your `update` function e.g.

```fsharp
| LoadCustomer customerId ->
    model, loadCustomer customerId
```

## **I'm using the minimal template** (Raw HTTP)
This recipe shows how to create a GET endpoint on the server and consume it on the client using the Fetch API.

### On the Server
#### 1. Write implementation
Create a function that implements the back-end service that you require. Use standard functions to read from databases or other external sources as required.
```fsharp
open Saturn
open FSharp.Control.Tasks

/// Loads a customer from the DB and returns as a Customer in json.
let loadCustomer (customerId:int) next ctx = task {
    let customer = { Name = "My Customer" }
    return! json customer next ctx
}
```
> Note how we parameterise this function to take in the `customerId` as the first argument. Any parameters you need should be supplied in this manner. If you do not need any parameters, just omit them and leave the `next` and `ctx` ones.

> This example does not cover dealing with "missing" data e.g. invalid customer ID is found.

#### 2.Expose your function
Tie the function into the router with a route.

```fsharp
let webApp = router {
    getf "/api/customer/%i" loadCustomer // Add this
}
```

> Note the use of `getf` rather than `get`. If you do not need any parameters, just use `get`. See [here](https://saturnframework.org/reference/Saturn/saturn-router-routerbuilder.html) for reference docs on the use of the Saturn router.

#### 3. Test the endpoint (optional)
Test out your endpoint using e.g. web browser / Postman / REST Client for VS Code etc.

### On the client
#### 1. Call the endpoint
Create a new function `loadCustomer` that will call the endpoint.

```fsharp
let loadCustomer customerId =
    let loadCustomer () = Fetch.get<unit, Customer> (sprintf "/api/customer/%i" customerId)
    Cmd.OfPromise.perform loadCustomer () CustomerLoaded
```

> Note the final value supplied, `CustomerLoaded`. This is the `Msg` case that will be sent into the
> Elmish loop *once the call returns*, with the returned data. It should take in a value that
> matches the type returned by the Server e.g. `CustomerLoaded of Customer`. See [here](mvu-roundtrip.md)
> for more information.

An alternative (and slightly more succinct) way of writing this is:

```fsharp
let loadCustomer customerId =
    let loadCustomer = sprintf "/api/customer/%i" >> Fetch.get<unit, Customer>
    Cmd.OfPromise.perform loadCustomer customerId CustomerLoaded
```

This can now be called from within your `update` function e.g.

```fsharp
| LoadCustomer customerId ->
    model, loadCustomer customerId
```