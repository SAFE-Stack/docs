# Data sharing with Fable.Remoting
Alongside raw HTTP, you can also use [Fable.Remoting](https://github.com/Zaid-Ajaj/Fable.Remoting), which provides an RPC-style mechanism for calling server endpoints. With Remoting, you don't need to worry about the details of serialization or of how to consume the endpoint - instead, remoting lets you define you client-server interactions as a shared *type* that is commonly referred to as a *protocol* or *contract*.

## 1. Define a protocol
Each field of the record is either of type `Async<T>` or a function that returns `Async<T>`, for example:
```fsharp
type ICustomerApi = {
    getCustomers : unit -> Async<Customer list>
    findCustomerByName : string -> Async<Customer option>
}
```
The supported types used within the protocol can be any F# type: primitive values (int, string, DateTime, etc.), records, options, discriminated unions or collections etc.

## 2. Implement the protocol on the server
On the server you would implement the protocol as follows:
```fsharp
let getCustomers() =
    async {
        return [
            { Id = 1; Name = "John Doe" }
            { Id = 2; Name = "Jane Smith" } ]
    }

let findCustomerByName (name: string) = 
    async {
        let! allCustomers = getCustomers()
        return allCustomers |> List.tryFind (fun c -> c.Name = name)
    }


let customerApi : ICustomerApi = {
    getCustomers = getCustomers
    findCustomerByName = findCustomerByName
}
```

## 3. Consume the protocol on the client
After [exposing an HttpHandler](https://zaid-ajaj.github.io/Fable.Remoting/#/server-setup/saturn) from `customerApi` you can start calling the API from the client. 

```fsharp
let api = Remoting.createApi() |> Remoting.buildProxy<ICustomerApi>

async {
    let! customers = api.getCustomers()
    for customer in customers do
        printfn "#%d => %s" customer.Id customer.Name
}
```

Notice here, there is no need to configure routes or JSON serialization, worry about HTTP verbs, or even involve yourself with the Giraffe pipeline. If you open your browser network tab, you can easily [inspect](https://zaid-ajaj.github.io/Fable.Remoting/#/advanced/raw-http-communication) what remoting is doing behind the scenes. 