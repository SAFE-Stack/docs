# How Do I Use LiteDB?
The default template uses in-memory storage. This recipe will show you how to replace the in-memory storage with [LiteDB](https://github.com/mbdavid/LiteDB) in the form of [LiteDB.FSharp](https://github.com/Zaid-Ajaj/LiteDB.FSharp).

> If you're using the minimal template, the first steps will show you how to add a LiteDB database; the remaining section of this recipe are designed to work off the default template's starter app.

#### 1. Add LiteDB.FSharp
Add the [LiteDB.FSharp](https://www.nuget.org/packages/LiteDB.FSharp/) NuGet package to the [server project](./../package-management/add-nuget-package-to-server.md).

#### 2. Remove the existing Storage repository
Delete the existing in-memory `Storage ()` type in `Server.fs` that uses an in-memory `ResizeArray`() to store data, as well as the reference to it directly below and the priming code:

```fsharp
// Delete everything from here...
type Storage () =
// down to here...
storage.AddTodo(Todo.create "Ship it !!!") |> ignore
```

#### 3. Create the database
Create a new `Storage` module in its place. To start with, create a private value that stores a handle to the database.

```fsharp
open LiteDB.FSharp
open LiteDB

module Storage =
    let private database =
        let mapper = FSharpBsonMapper()
        let connStr = "Filename=Todo.db;mode=Exclusive"
        new LiteDatabase (connStr, mapper)
```

> LiteDb is a file-based database, and will create the file if it does not exist automatically.

This will create a database file `Todo.db` in the `Server` folder. The option `mode=Exclusive` is added for MacOS support (see this [issue](https://github.com/mbdavid/LiteDB/issues/787)).

> See [here](https://www.litedb.org/docs/connection-string/) for more information on connection string arguments.

> See the [official docs](https://www.litedb.org/docs) for details on constructor arguments.

#### 4. Implement the rest of the repository
Add the following code inside the `Storage` module to handle insertion and retrieval of todo items.

```fsharp
    let private todos = database.GetCollection<Todo> "todos"

    /// Retrieves all todo items.
    let getTodos () =
        todos.FindAll () |> List.ofSeq

    /// Tries to add a todo item to the collection.
    let addTodo (todo:Todo) =
        if Todo.isValid todo.Description then
            todos.Insert todo |> ignore
            Ok ()
        else
            Error "Invalid todo"

    /// Primes the todo collection with some starting data, if required.
    let initialise () =
        if not (database.CollectionExists todos.Name) then
            addTodo (Todo.create "Create new SAFE project") |> ignore
            addTodo (Todo.create "Write your app") |> ignore
            addTodo (Todo.create "Ship it !!!") |> ignore
```

#### 5. Initialise the database
Underneath the `Storage` module, make sure you call the `initialise` function to prime the database with default data.

```fsharp
Storage.initialise ()
```

#### 6. Fix up compiler errors
Inside the `todosApi`, correct any references to the storage repository functions, e.g.

```fsharp
getTodos = fun () -> async { return storage.GetTodos() } // before
getTodos = fun () -> async { return Storage.getTodos() } // after
```

#### 7. Make Todo compatible with LiteDb
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
