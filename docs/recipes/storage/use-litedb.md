# How Do I Use LiteDB?
The default template uses in-memory storage. This recipe will show you how to replace the in-memory storage with [LiteDB](https://github.com/mbdavid/LiteDB) in the form of [LiteDB.FSharp](https://github.com/Zaid-Ajaj/LiteDB.FSharp).

#### 1. Add LiteDB.FSharp
Add the [LiteDB.FSharp](https://www.nuget.org/packages/LiteDB.FSharp/) NuGet package to the [server project](./../package-management/add-nuget-package-to-server.md).

#### 2. Create the database
Replace the use of the `ResizeArray` in the `Storage` type with a database and collection:

```fsharp
open LiteDB.FSharp
open LiteDB

type Storage () =
    let database =
        let mapper = FSharpBsonMapper()
        let connStr = "Filename=Todo.db;mode=Exclusive"
        new LiteDatabase (connStr, mapper)
    let todos = database.GetCollection<Todo> "todos"
```

> LiteDb is a file-based database, and will create the file if it does not exist automatically.

This will create a database file `Todo.db` in the `Server` folder. The option `mode=Exclusive` is added for MacOS support (see this [issue](https://github.com/mbdavid/LiteDB/issues/787)).

> See [here](https://www.litedb.org/docs/connection-string/) for more information on connection string arguments.

> See the [official docs](https://www.litedb.org/docs) for details on constructor arguments.

#### 3. Implement the rest of the repository
Replace the implementations of `GetTodos` and `AddTodo` as follows:

```fsharp
    /// Retrieves all todo items.
    member _.GetTodos () =
        todos.FindAll () |> List.ofSeq

    /// Tries to add a todo item to the collection.
    member _.AddTodo (todo:Todo) =
        if Todo.isValid todo.Description then
            todos.Insert todo |> ignore
            Ok ()
        else
            Error "Invalid todo"
```

#### 4. Initialise the database
Modify the existing "priming" so that it first checks if there are any records in the database before inserting data:

```fsharp
if storage.GetTodos() |> Seq.isEmpty then
    storage.AddTodo(Todo.create "Create new SAFE project") |> ignore
    storage.AddTodo(Todo.create "Write your app") |> ignore
    storage.AddTodo(Todo.create "Ship it !!!") |> ignore
```

#### 5. Make Todo compatible with LiteDb
Add the [CLIMutable](https://github.com/MicrosoftDocs/visualfsharpdocs/blob/master/docs/conceptual/core.climutableattribute-class-%5Bfsharp%5D.md) attribute to the `Todo` record in `Shared.fs`

```fsharp
[<CLIMutable>]
type Todo =
    { Id : Guid
      Description : string }
```

> This is required to allow LiteDB to hydrate (read) data into F# records.

#### All Done!
* Run the application.
* You will see that a database has been created in the Server folder and that you are presented with the standard TODO list.
* Add an item and restart the application; observe that your data is still there.
