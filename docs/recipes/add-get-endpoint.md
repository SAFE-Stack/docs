# How do I add a GET endpoint to Saturn?

A GET endpoint allows us to request a body of data from the server for the client.

In order to add a GET endpoint to Saturn, we must first add a router to the application. If you are starting with a SAFE template this may have already been done for you.

```fsharp
let app =
    application {
        use_router webApp
    }
```

This router itself is an expression which allows us to build up a list of endpoints.

We can add GET endpoints which take any number and type of arguments that can be parsed from the URL.

```fsharp
open FSharp.Control.Tasks.V2
open Microsoft.AspNetCore.Http

let hello = text "Hello from SAFE!" 

let helloName name = text (sprintf "Hello %s" name)

let helloQuery next (ctx : HttpContext) = task {
    match ctx.GetQueryStringValue "Name" with
    | Ok name -> return! text (sprintf "Hello %s" name) next ctx
    | Error e -> return! text "Hello from SAFE!" next ctx
} 

type Customer = { Name : string }
let helloBoundQuery next (ctx : HttpContext) = task {
     let customer =  ctx.BindQueryString<Customer>()
     return! text (sprintf "Hello %s" customer.Name) next ctx
 } 

let webApp =
    router {
        get "/api/hello" hello
        getf "/api/hello/%s" helloName
        get "/api/helloQuery" helloQuery
        get "/api/helloBoundQuery" helloBoundQuery
    }
```