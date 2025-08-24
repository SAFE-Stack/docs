# How Do I Share Code Types Between the Client and the Server?
SAFE Stack makes it really simple and easy to share code between the client and the server, since both of them are written in F#. The client side is transpiled into JavaScript by Fable, whilst the server side is compiled down to .NET CIL. Serialization between both happens in the background, so you don't have to worry about it.

---

## Types
Let's say the you have the following type in `src/Server/Server.fs`:
```fsharp
type Customer =
    { Id : Guid
      Name : string
      Email : string
      PhoneNumber : string }
```

## Values and Functions
And you have the following function that is used to validate this Customer type in `src/Client/Index.fs`:
```fsharp
let customerIsValid customer =
    (Guid.Empty = customer.Id
    || String.IsNullOrEmpty customer.Name
    || String.IsNullOrEmpty customer.Email
    || String.IsNullOrEmpty customer.PhoneNumber)
    |> not
```

## Shared
If at any point you realise you need to use both the `Customer` type and the `customerIsValid` function both in the Client and the Server, all you need to do is to move both of them to `Shared` project. You can either put them in the `Shared.fs` file, or create your own file in the Shared project (eg. `Customer.fs`). After this, you will be able to use both the `Customer` type and the `customerIsValid` function in both the Client and the Server.

## Serialization
SAFE comes out of the box with [Fable.Remoting] or [Thoth] for serialization. These will handle the transport of data seamlessly for you.

## Considerations

> Be careful not to place code in `Shared.fs` that depends on a Client or Server-specific dependency. If your code depends on `Fable` for example, in most cases it will not be suitable to place it in Shared, since it can only be used in Client. Similarly, if your types rely on .NET specific types in e.g. the framework class library (FCL), beware. Fable [has built-in mappings](https://fable.io/docs/dotnet/compatibility.html) for popular .NET types e.g. `System.DateTime` and `System.Math`, but you will have to write your own mappers otherwise.
