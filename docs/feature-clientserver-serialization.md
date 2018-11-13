## Serialization basics with Thoth
When using basic HTTP communication between the client and server, you'll need to consider how to deserialize data from JSON to F# types.

In order to guarantee that the serialization / deserialization routines between client and server are compatible, you should replace the JSON converter in Giraffe / Saturn with the [Thoth](https://mangelmaxime.github.io/Thoth/index.html) library's serializer. This is the same library as that used in Fable for deserialization, and so will work seamlessly together.

```fsharp
let configureSerialization (services:IServiceCollection) =
    services.AddSingleton<Giraffe.Serialization.Json.IJsonSerializer>(Thoth.Json.Giraffe.ThothSerializer())
```

If you are using the [SAFE Template](safe-template), this will automatically be done for you in `Server.fs`.

## Approaches to deserialization
Fable 2 uses the Thoth library for JSON deserialization, which makes use of *decoders* to convert JSON into F# values. There are generally two main approaches to take when doing this: **automatic** and **manual** decoders.

Assume the following Customer record for the remaining examples.

```fsharp
type Customer =
    { Id : int
      Name : string }
```

### Automatic Decoders
Automatic decoders are the quickest and easier way to deserialize data. It works by Thoth trying to decode JSON automatically from a raw string to an F# type using automatic mapping rules. In the sample below, we fetch data from the `/api/customers` endpoint and have Thoth create a strongly-typed Decoder for a `Customer` array.

```fsharp
fetchAs<Customer []> "/api/customers" (Decode.Auto.generateDecoder()) []
```

If the serialization fails, Thoth will create an `Error` (rather than `Ok`) value for this.

Be aware that automatic decoders are designed to work with primitives, collections, F# records, tuples and discriminated unions but cannot deserialize classes.

#### Improving efficiency with cached decoders
You can reuse decoders when you know you'll be calling them often:

```fsharp
// let-bound value that exists outside of the update function
let customerDecoder = Decode.Auto.generateDecoder<Customer>()

// inside the update function
Fetch.fetchAs (sprintf "api/customers") (Decode.array customerDecoder [])
```

Notice how the decoder is bound to a single Customer, and not an array. This way, we can also reuse the decoder on other routes, for example `api/customers/1` which would return a single Customer object rather than a collection.

### Manual Decoders
Manual decoders give you total control over how you rehydrate an object from JSON. Use them when:

* The JSON does not directly map 1:1 with your F# types
* You want flexibility to evolve JSON and F# types independently
* You are calling an external service and need fine-grained control over the deserialization process
* You are using F# on the client and another language on the server

You create a manual decoder as follows:

```fsharp
let customerDecoder : Decoder<Customer> =
    Decode.object
        (fun get ->
            { Id = get.Required.Field "id" Decode.int
              Name = get.Optional.Field "customerName" Decode.string |> Option.defaultValue "" })
```

You can now replace the automatically generated decoder from earlier. You can also "manually" decode JSON to Customers as follows:

```fsharp
Decode.fromString customerDecoder """{ "id": 67, "customerName": "Joe Bloggs" }"""
```

If decoding fails on any field, an error case will be returned.