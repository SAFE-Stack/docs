# How do I handle client / server roundtrips with MVU?
This recipe shows what steps you need to take to store new data on the client using the MVU pattern, which is typically read from the Server. You will learn the steps required to update the model, update and view functions to handle a button click which requests data from the server and handles the response.

## In Shared
#### 1. Create shared domain
Create a type in the Shared project which will act as the contract type between client and server. As SAFE compiles F# into JavaScript for you, you only need a single definition which will automatically be shared.
```fsharp
type Customer = { Name : string }
```

## On the Client
#### 1. Create message pairs
Modify the `Msg` type to have two new messages:

```fsharp
    type Msg =
        // other messages ...
        | LoadCustomer of customerId:int // Add this
        | CustomerLoaded of Customer // Add this
```

> You will see that this symmetrical pattern is often followed in MVU:
>
> * A *command* to initiate a call to the server for some data (**Load**Customer)
> * An *event* with the result of calling the command (Customer**Loaded**)

#### 2. Update the Model
Update the Model to store the Customer once it is loaded:
```fsharp
type Model =
    { // ...
      TheCustomer : Customer option }
```

> Make `TheCustomer` optional so that it can be initialised as `None` (see next step).

#### 3. Update the Init function
Update the `init` function to provide default data
```fsharp
let model =
    { // ...
      TheCustomer = None }
```
#### 4. Update the View
Update your view to initiate the `LoadCustomer` event. Here, we create a button that will start loading customer 42 on click:
```fsharp
let view model dispatch =
    div [] [
        // ...
        button [ OnClick (fun _ -> dispatch (LoadCustomer 42)) ] [ str "Load Customer"]
    ]
```
#### 5. Handle the Update
Modify the `update` function to handle the new messages:
```fsharp
let update msg model =
    match msg with
    // ....
    | LoadCustomer customerId ->
        // Implementation to connect to the server to be defined.
    | CustomerLoaded c ->
        { model with TheCustomer = Some c }, Cmd.none
```

> The code to fire off the message to the server differs depending on the client / server communication you are using and normally whether you are reading or writing data. See [here](messaging.md) for more information.