Using F# on both client and server is at the core of the SAFE stack, as it simplifies the way we think about building web applications by using the same language, idioms and in many cases sharing our code and domain models.

However, building a client and a server app requires a fundamentally different way of thinking. On the server side we build [stateless APIs in Saturn](component-saturn) that map HTTP requests to internal functionality, whereas on the frontend we use the Elmish model, implementing the [model-view-update pattern](component-elmish): a stateful pattern that lets us think about the application state as it evolves while the application running. 

Even though we use the same language across platforms, applying different these two different programming models forces us to switch our way of thinking back and forth when writing code for the client and for the server. This is where the [Elmish.Bridge](https://github.com/Nhowka/Elmish.Bridge) library comes into play: it brings the Elmish programming model *to the server* and unifies the way we write the application as a whole.

## How does Elmish work on the server?
Think of Elmish on the server as the model-view-update pattern but *without the view part*. Instead, you only need to implement `init` and `update` functions to manage the *server* state as it evolves while the server is running.

* Server state can contain data that is relevant to a single or all clients
* The dispatch loop running on the server is connected to the dispatch loop on the client via a *persistent stateful websocket connection*
* The `update` functions on client and server can exchange data via message passing.

## A simple example
Let's see a simple example of how this might work in practice:

```fsharp
// Client-side 
let update msg state = 
    match msg with 
    | LoadUsers -> 
        // send the message to the server
        Bridge.Send(ServerMsg.LoadUsers)
        state, Cmd.none
    | UsersLoaded users ->
        // receive message from the server
        let nextState = { state with Users = users }
        nextState, Cmd.none

// Server-side
let update clientDispatch msg state = 
    match msg with 
    | ServerMsg.LoadUsers ->
        let loadUsersCmd = 
            Cmd.ofAsync 
                getUsersFromDb    // unit -> Async<User list>
                ()                // input arg = unit
                UsersLoadedFromDb // User list -> ServerMsg
                DoNothing         // ServerMsg
        state, loadUsersCmd
                     
    | ServerMsg.UsersLoadedFromDbSuccess users ->
        // answer the current connected client with data
        clientDispatch (ClientMsg.UsersLoaded users)
        state, Cmd.none

    | ServerMsg.DoNothing ->
        state, Cmd.none
```

The above example mimics what would have been a `GET` request to the server to get user data from database. However, now the client sends a fire-and-forget message to the server to load users, and at some point the server messages the current client back with the results. Notice that the server could have decided to do other things than just messaging the client back: for example, it could have broadcasted the same message to other clients updating their local state of the users.  

## When to use Elmish.Bridge
There are many scenarios where it makes sense to use Elmish.Bridge:

* Chat-like applications with many connected users through many channels
* Syncing price data in real-time while viewing ticket prices
* Multiplayer games that need real-time update of game states
* Other applications of web sockets through an Elmish model

## Things to consider
The biggest distinction between using this and "raw" Saturn is that your web server becomes a stateful service. This introduces several differences for application design.

1. The server state has a lifespan equal to the that of the process under which the server instance is running. This means if the server application restarts then the server state will be reset.

2. The server state is *local to the server instance*. This means that if you run multiple web servers, they won't be sharing the same server state by default.

As of now there is no built-in persistence for the state, but you can implement this yourself using any number of persistance layers such as Redis Cache, Azure Tables or Blobs etc.

In addition Elmish.Bridge does not use standard HTTP verbs for communication, but rather websockets. Therefore, it is not a suitable technology for an open web server that can serve requests from other sources than Elmish.Bridge clients.

## Learn more about Elmish.Bridge
Head over to [Elmish.Bridge](https://github.com/Nhowka/Elmish.Bridge) to learn more. 