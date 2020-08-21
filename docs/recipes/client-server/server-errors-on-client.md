# How Do I Handle Server Exceptions on the Client?
SAFE Stack makes it easy to catch and handle exceptions raised by the server on the client. Though the way we make a call to the server from the client is different between the standard and the minimal template, the way we handle server errors on the client is the same in principle.

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

The following steps will vary depending on whether you’re using the standard template or the minimal one.

#### 4. Connect Server Errors to Elmish

We now have to connect up the server response to the new message we created. Elmish has support for this through the `either` Cmd functions (instead of the `perform` functions). Make the following changes to your server call:

##### I Am Using the Standard Template

```fsharp
let cmd = Cmd.OfAsync.perform todosApi.getTodos () GotTodos
```

…and replace it with the following:

```fsharp
let cmd = Cmd.OfAsync.either todosApi.getTodos () GotTodos GotError
```

##### I Am Using the Minimal Template

```fsharp
let cmd = Cmd.OfPromise.perform getHello () GotHello
```

…and replace it with the following:

```fsharp
let cmd = Cmd.OfPromise.either getHello () GotHello GotError
```

## Done!
Now, if you get an exception from the Server, its message will be added to the `Errors` field of the `Model` type. Instead of throwing the error, you can now display a meaningful text to the user like so:

```fsharp
[ for msg in errorMessages do
    p [] [ str msg ]
]
```