# How do I load data from server to client in MVU?
This recipe shows what steps you need to take to store new data on the client using the MVU pattern, which is typically read from the Server.

In this recipe, we see the steps required to add a Customer type and the associated messages within the MVU loop.

## Shared Data
1. Create a type in the Shared project which will act as the contract between client and server:
```fsharp
type Customer = { Name : string }
```

> Put this type in Shared so that it can be accessed by the Server. If the type only lives on the Client, you can put the type directly in the Client project.

## On the Client
1. Modify the `Msg` type to have two new messages:
    ```fsharp
    type Msg =
        | GotHello of string
        | LoadCustomer of int // Add this
        | CustomerLoaded of Customer // Add this
    ```
* `LoadCustomer` represents the command to fire of the call to the server
* `CustomerLoaded` represents the event when the server returns with the customer.

> You will see that this symmetrical pattern is often followed:
> * A command to initiate a call to the server for some data
> * An event with the result of the command

2. Update your Model to store the Customer once it is loaded:
    ```fsharp
    type Model =
        { Hello: string
          TheCustomer : Customer option }
    ```

> We make `TheCustomer` optional so that it can be initialised as `None` (see next step).

3. Update your `init` function to provide default data:
    ```fsharp
    let model =
        { Hello = ""
        TheCustomer = None }
    ```
4. Update your view to initiate the `LoadCustomer` event. Here, we create a button that will dispatch a load of customer 42 on click:
    ```fsharp
    let view model dispatch =
        div [] [
            // ...
            button [ OnClick (fun _ -> dispatch (LoadCustomer 42)) ] [ str "Load Customer"]
        ]
    ```
5. Update the `update` function to handle the new messages:
    ```fsharp
    let update msg model =
        match msg with
        // ....
        | LoadCustomer customerId ->
            // Fire off code to load a customer here!
        | CustomerLoaded c ->
            { model with TheCustomer = Some c }, Cmd.none
    ```

> The code to fire off the message to the server differs depending on the client / server communication you are using and normally whether you are reading or writing data. See [here] for more information.