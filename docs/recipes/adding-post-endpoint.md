# How do I add a POST endpoint to Saturn?

A POST endpoint allows us to send a body of data from the client to the server.

This is often a serialised object which we wish to bind to a model.

In order to add a POST endpoint to Saturn, we must first add a router to the application. If you are starting with a SAFE template this may have already been done for you.

```fsharp
let app =
    application {
        use_router webApp
    }
```

This router itself is an expression which allows us to build up a list of endpoints.

We can add POST endpoints which take any number and type of arguments which are parsed from the URL.

```fsharp
let messageHandler next ctx = task { return! text "A response" next ctx }
let messageIntHandler intArg next ctx  = task {
    return! text (sprintf "Response %i" intArg) next ctx
}

let webApp =
    router {
        post "/api/message" messageHandler
        postf "/api/message/%i" messageIntHandler
    }
```

When retrieving the request body, we can bind it to a model using an ASP .NET  helper method. This uses the appropriate deserialiser as specified by the Content Type provided in the request header.

```fsharp
open Microsoft.AspNetCore.Http

type Model =
 { Name : string
   Age : int option }

let messageHandler next (ctx : HttpContext) = task { 
    let! content = ctx.BindModelAsync<Model>()
    return! text (sprintf "Hello %s" content.Name) next ctx
}


```