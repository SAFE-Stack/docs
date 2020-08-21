# How Do I Handle Server Errors on the Client?
SAFE Stack makes it easy to handle Server errors on the Client. Though the way we make a call to the Server from the Client is different between the standard and the minimal template, the way we handle Server errors on the Client is the same in principle. However, there is a slight deviation in this recipe between the two versions of the template just to keep the steps accurate (e.g. the names of some functions).

## The Shared Steps
#### 1. The Errors Field
Find the `Model` type in `src/Client/Client.fs` and add it the following `Errors` field:
```fsharp
type Model =
    { ... // the rest of the fields
      Errors: string list }
```
This will be used to store the error messages that we receive from the server.

#### 2. The Messages
Also, go ahead and add the following case to the `Msg` type:
```fsharp
| GotError of exn
```
This is the case that will be used to handle any errors we get back from the server after making a request.

#### 3. Handle the Errors Field
Bind an empty list to the `Errors` field of the `model` record inside the `init` function. Otherwise, your code will not compile:
```fsharp
let model =
    { ... // the rest of the fields
      Errors = [] }
```

#### 4. Handle the GotError Case
Add the following line to the end of the pattern match inside the `update` function. Make sure it’s the last case of the match:
```
| GotError er ->
    { model with Errors = er.Message :: model.Errors }, Cmd.none
```
This case will add the message of the error that comes back from the server to the `Errors` field of our `Model`.

---
> The following steps will vary depending on whether you’re using the standard template or the minimal one. Follow the steps that corresponds to the version of the SAFE template you’re using and ignore the rest.
---

## I Am Using the Standard Template
Find the following line inside the `init` function:
```fsharp
let cmd = Cmd.OfAsync.perform todosApi.getTodos () GotTodos
```
…and replace it with the following:
```fsharp
let cmd = Cmd.OfAsync.either todosApi.getTodos () GotTodos GotError
```


## I am Using the Minimal Template
Find the following line inside the `init` function:
```fsharp
let cmd = Cmd.OfPromise.perform getHello () GotHello
```
…and replace it with the following:
```fsharp
let cmd = Cmd.OfPromise.either getHello () GotHello GotError
```


## Done!
Now, if you get an exception from the Server, its message will be added to the `Errors` field of the `Model` type. Instead of throwing the error, you can now display a meaningful text to the user by making a pattern match on this field inside your `view` function like so:
```fsharp
yield! 
    match model.Errors with
    | [] -> []
    | errorMessages -> 
        [ for msg in errorMessages do 
            p [] [ str msg ] ]
```

#drafts
