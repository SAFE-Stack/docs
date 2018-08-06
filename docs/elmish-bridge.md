# Elmish.Bridge - Introduction

Using F# both on client and server is at the core of the SAFE stack, it simplifies the way we think about building web applications because we are using the same language, same idioms and in many cases sharing our code and domain models. 

However, building a client and a server app requires a fundamentally different way of thinking. On the backend we build stateless api's that map HTTP requests to internal functionality whereas on the frontend, we have Elmish implementing model-view-update: a stateful pattern that lets us think about the application state as it evolves while the application running. 

Even though we are using the same language across platforms, applying different programming models (Stateless vs. Stateful) forces us to switch our way of thinking back and forth when writing code for the client and for the server. This is where the [Elmish.Bridge](https://github.com/Nhowka/Elmish.Bridge) library comes into play: it brings the Elmish programming model *to the server* and unifies the way we write the application as a whole.  

# Elmish ... on the server?

You might ask: *"Isn't Elmish a client thing, how does that make sense on the server?"* You can think of it as the model-view-update pattern without the view part: you will have both `init` and `update` functions that manage the *Server* state as it evolves while the server is running, that state might contain data that is relevant to a single client or all clients. The dispatch loop running on the server will be connected to the dispatch loop on the client via a persistent stateful websocket connection, the `update` functions will be able exchange data via message passing. *"Ok, enough talk, show me the code already!"*

```fs
// Client-side 
let update msg state = 
    match msg with 
    | LoadUsers -> 
        // send the message to the server
        Bridge.Send(ServerMsg.LoadUsers)
        state, Cmd.none
    | UsersLoaded users -> 
        let nextState = { state with Users = users }
        nextState, Cmd.none

// Server-side
// notice the ability to call the client dispatch
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
The above example mimics what would have been a `GET` request to the server to get user data from database, only now the client sends a (fire-and-forget) message and at some point the server would message the current client back with results. Notice that the server could have decided to do other things than just messaging the client back: for example it could have broadcasted the same message to other clients updating their local state of the users.  

# Learn More 

This was a bit of taste for Elmish.Bridge, there are many scenario's where it makes sense to use it:
 - Chat-like applications (Many connected users through many channels)
 - Syncing price data in real-time while viewing ticket prices
 - Multiplayer games that need real-time update of game states 
 - Other applications of web sockets but through the Elmish model 

Head over to [Elmish.Bridge](https://github.com/Nhowka/Elmish.Bridge) to learn more. 