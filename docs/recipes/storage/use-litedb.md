# How Do I Use LiteDB?
The default template uses in-memory storage. This recipe will show you how to replace the in-memory storage with [LiteDB](https://github.com/mbdavid/LiteDB) in the form of [LiteDB.FSharp](https://github.com/Zaid-Ajaj/LiteDB.FSharp).

The focus of this recipe is quick results while remaining idiomatic. An extended version of this recipe should include deletion and updating of records.

All `FSharp` code is added to `Server/Server.fs` and is presented in order from top-to-bottom.

### Using LiteDB.FSharp

1. [Add the LiteDB.FSharp NuGet package to the solution](./../package-management/add-nuget-package-to-server.md).
1. Open `LiteDB` and `LiteDB.FSharp` in `Server.fs`
```fsharp
open LiteDB.FSharp
open LiteDB
```

### Make Todo Serializable
1. Add the [CLIMutable](https://github.com/MicrosoftDocs/visualfsharpdocs/blob/master/docs/conceptual/core.climutableattribute-class-%5Bfsharp%5D.md) attribute to `type Todo` in `Shared/Shared.fs`
```fsharp
[<CLIMutable>]
type Todo =
    { Id : Guid
      Description : string }
```

### Database Creation
1. [Create a LiteDatabase](https://www.litedb.org/docs/connection-string/)
```
let database dbName =
    let mapper = FSharpBsonMapper()
    let dbFile = sprintf "%s.db" dbName
    let connStr = sprintf "Filename=%s;mode=Exclusive" dbFile
    new LiteDatabase( connStr, mapper )
```
- At this time, I cannot find an official reference to the `LiteDB` constructor. See the [official docs](https://www.litedb.org/docs) for details on constructor arguments.
- The arguments in use here are reasonable defaults. The database file `Todo.db` will be created in the `Server` folder. We're adding the option `mode=Exclusive` to add support for MacOS (see this [issue](https://github.com/mbdavid/LiteDB/issues/787)).

### Storage Adapter
1. Modify the existing `Storage` type to use a `LiteDatabase`
```fsharp
type Storage (db : LiteDatabase) as this =
    let collection = "todos"
    let todos = db.GetCollection<Todo> collection

    do
        if not (db.CollectionExists collection) then
            this.AddTodo(Todo.create "Create new SAFE project") |> ignore
            this.AddTodo(Todo.create "Write your app") |> ignore
            this.AddTodo(Todo.create "Ship it !!!") |> ignore

    member __.GetTodos () =
        todos.FindAll() |> List.ofSeq

    member __.AddTodo (todo: Todo) =
        if Todo.isValid todo.Description then
            todos.Insert(todo) |> ignore
            Ok ()
        else Error "Invalid todo"
```
We have:
- Added a constructor argument to pass the database
- Redefined `todos` to be a `LiteCollection`
- Added initialization code into a `do` section. We only want to add the default records the first time
we create the database.
- Updated `GetTodos` and `AddTodo` to use the database

### ITodosApi Instantiation
1. Modify `todosApi` to use a storage argument rather than the original global:
```fsharp
let todosApi (storage : Storage) =
    { getTodos = fun () -> async { return storage.GetTodos() }
      addTodo =
        fun todo -> async {
            match storage.AddTodo todo with
            | Ok () -> return todo
            | Error e -> return failwith e
        } }
```
- This is a minor piece of code tidying. After the database initialization was moved into the `Storage` type, this was the only
reference left to the global `storage` function.

### Top-level Initialization
1. Instantiate `database`, `Storage` and `ITodosApi` during top-level instantiation
```fsharp
let webApp =
    Remoting.createApi()
    |> Remoting.withRouteBuilder Route.builder
    |> Remoting.fromValue (database "Todo" |> Storage |> todosApi)
    |> Remoting.buildHttpHandler
```

