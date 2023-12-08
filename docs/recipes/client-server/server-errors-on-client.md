# How Do I Handle Server Exceptions on the Client?
SAFE Stack makes it easy to catch and handle exceptions raised by the server on the client.

---

#### 1. Update the Model
Update the model to store the error details that we receive from the server. Find the `Model` type in `src/Client/Index.fs` and add it the following `Errors` field:

```fsharp
type Model =
    { ... // the rest of the fields
      Errors: string list }
```

Now, bind an empty list to the field record inside the `init` function:

```fsharp
let model =
    { ... // the rest of the fields
      Errors = [] }
```

#### 2. Add an Error Message Handler
We now add a new message to handle errors that we get back from the server after making a request. Add the following case to the `Msg` type:

```fsharp
type Msg =
    | ... // other message cases
    | GotError of exn
```

#### 3. Handle the new Message
In this simple example, we will simply capture the `Message` of the exception. Add the following line to the end of the pattern match inside the `update` function:

```fsharp
| GotError ex ->
    { model with Errors = ex.Message :: model.Errors }, Cmd.none
```

#### 4. Connect Server Errors to Elmish

We now have to connect up the server response to the new message we created. Elmish has support for this through the `either` Cmd functions (instead of the `perform` functions). Make the following changes to your server call:

```fsharp
let cmd = Cmd.OfAsync.perform todosApi.addTodo todo AddedTodo 
```

…and replace it with the following:

```fsharp
let cmd = Cmd.OfAsync.either todosApi.addTodo todo AddedTodo GotError
```

## Done!
Now, if you get an exception from the Server, its message will be added to the `Errors` field of the `Model` type. Instead of throwing the error, you can now display a meaningful text to the user like so:

In the function `todoAction` in `src/Client/Index.fs` add the following:

```fsharp
Html.button [ 
//other button properties
]
for msg in model.Errors do
    Html.p msg
```

## Test
In the `todosApi` in `src/Server/Server.fs` replace the `addTodo` with the following:

```fsharp
addTodo =
    fun todo -> async {
        failwith "Something went wrong"
        return
            match Storage.addTodo todo with
            | Ok() -> todo
            | Error e -> failwith e
    }
```

and when you try to add a todo then you will see the error message from the server.