# How Do I Share Code Types Between the Client and the Server?
SAFE Stack makes it really simple and easy to share code between the Client and the Server.
 
### Types/Values
Let’s say the you have the following type in `src/Server/Server.fs`:
```fsharp
type Customer =
    { Id : Guid
      Name : string
      Email : string
      PhoneNumber : string }
```

### Functions
And you have the following function that is used to validate this Customer type in `src/Client/Index.fs`:
```fsharp
let customerIsValid customer =
    (Guid.Empty = customer.Id
    || String.IsNullOrEmpty customer.Name
    || String.IsNullOrEmpty customer.Email
    || String.IsNullOrEmpty customer.PhoneNumber)
    |> not
```

### Shared
If at any point you realise you need to use both the `Customer` type and the `customerIsValid` function both in the Client and the Server, all you need to do is to move both of them to `Shared` project. You can either put them in the `Shared.fs` file, or create your own file in the Shared project (eg. `Customer.fs`). After this, you will be able to use both the `Customer` type and the `customerIsValid` function in both the Client and the Server.

### Considerations
Be careful not to place code in `Shared.fs` that depends on a Client or Server-specific dependency. If your code depends on `Fable` for example, in most cases it will not be suitable to place it in Shared, since it can only be used in Client.