# How do I read data from the server in a SAFE app?
This recipe shows how to create an endpoint on the server and hook up it up to the client. This recipe assumes that you have also followed [this] recipe in order to add the client-side messaging and model as well.

## I'm using the standard template (Fable Remoting)

## I'm using the minimal template (Raw HTTP)
This recipe shows how to create a GET endpoint on the server and consume it using the Fetch API.

### On the Server
1. Create a function that can load any data as required. Use functions to read from databases or other external sources as required.
```fsharp
open Saturn
open FSharp.Control.Tasks

/// Loads a customer from the DB and returns as a Customer in json.
let loadCustomer (customerId:int) next ctx = task {
    let! dbResponse = Database.loadCustomer customerId
    let customer = { Name = dbResponse.CustomerName }
    return! json customer next ctx
}
```
> Note how we parameterise this function to take in the `customerId` as the first argument. Any parameters you need should be supplied in this manner. If you do not need any parameters, just omit them and leave the `next` and `ctx` ones.

3. Add the function to your router to expose in the app.

```fsharp
let webApp = router {
    getf "/api/customer/%i" loadCustomer // Add this
}
```

> Note the use of `getf` rather than `get`. If do not need any parameters, just use `get`

**See [here](https://saturnframework.org/reference/Saturn/saturn-router-routerbuilder.html) for reference docs on the use of the Saturn router.**

3. Test out your endpoint using e.g. web browser / Postman / REST Client for VS Code etc.

### On the client
3. Create a new function `loadCustomer` that will call the endpoint:
```fsharp
let loadCustomer customerId =
    let loadCustomer () = Fetch.get<unit, Customer> (sprintf "/api/customer/%i" customerId)
    Cmd.OfPromise.perform loadCustomer () CustomerLoaded
```

An alternative (and slightly more succinct) way of writing this is:

```fsharp
let loadCustomer customerId =
    let loadCustomer = sprintf "/api/customer/%i" >> Fetch.get<unit, Customer>
    Cmd.OfPromise.perform loadCustomer customerId CustomerLoaded
```