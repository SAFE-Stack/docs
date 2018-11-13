# Client Server communication over HTTP
Communicating over raw HTTP using Saturn has three main steps.
## 1. Load your data
Start by creating a function in your server that returns some data:

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

Also note the `next` and `ctx` arguments. These are used by Giraffe as part of its [HTTP pipeline](https://github.com/giraffe-fsharp/Giraffe/blob/master/DOCUMENTATION.md#fundamentals) and are required by the `json` function (Note you can also use `Successful.Ok` instead of `json`, which will offer XML serialization as well).

## 2. Expose data through Saturn.
Now expose the api method using [Saturn's](https://saturnframework.github.io/docs/api/scope/) `router` construct and add it to your overall application scope:
```fsharp
let myApis = router {
    get "/api/customers/" getCustomers
}
```

For simple endpoints you may elect to embed the API call directly in the scope (and use partial application to omit the `next` and `ctx` arguments):
```fsharp
let myApis = router {
    get "/api/customers/" (json (loadCustomersFromDb()))
}
```

## 3. Consume the endpoint from the client.
Finally, call the endpoint from your client application.
```fsharp
promise {    
    let! customers = Fetch.fetchAs<Customer list> "api/customers" (Decode.Auto.generateDecoder()) []
    // do more with customers here...
}
```

Note the use of the `promise { }` computation expression. This behaves similarly to `async { }` blocks that you might already know, whilst the `fetchAs` function retrieves data from the HTTP endpoint specified. The JSON is deserialized a `Customer` array using an automatically-generated "decoder" (see the section on [serialization](feature-clientserver-serialization) for more information).
