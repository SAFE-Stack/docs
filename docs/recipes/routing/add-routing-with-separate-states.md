# How do I include multiple pages with their own state?
If your application has multiple separate components, there is no need to have one big, complex state that manages all the information for all components. In this recipe we separate the information of the todo list out of main ```Model```, and give it it's own route. We also add a "Page not found" page.

#### 1. Adding the Feliz router
To know which route a user is accessing, you can make use of the Feliz router. Use the following command to install the Feliz router in the client project.

```bash
dotnet paket add Feliz.Router -p Client
```
To include the router in the Client, add ``` open Feliz.Router``` at the top of index.fs.

#### 2.  Parsing URLs
The Feliz router parses URLs into a list of all the url segments; we can pattern match on these elements to give a page in our application. Create a function that finds a ```Url``` given an list of Url segments. Add a wildcard, so everything we cannot parse is routed to a NotFound Page.

```fsharp
let parseUrl url = 
    match url with
    | ["todo"] -> Url.TodoList
    | _ -> Url.NotFound
```

#### 3. Creating a module for the Todo list
To separate the logic and data related to the Todo list from the main model, we put it in a separate module. To do so, create a new file, TodoList.fs; make sure you place it above the Index.fs file; the Index module will depend on the TodoList module.
Move the following functions and types to the TodoList Module:
* Models
* Msg
* todosApi
* init
* update
* containerBox; rename this to view

#### 4. Adding a new Model to the Index page

Now that we removed the Todo model from the view, we can replace it with a new model that deals with routing. Create a new type ```Page```, with a case for the TodoList that holds a Todolist Model, and a case a notFound page.

```fsharp
type Page =
    | TodoList of TodoList.Model
    | NotFound 
```

Also add a union type for Url, which has a case for the TodoList and a NotFound case.
```fsharp
type Url =
    | TodoList
    | NotFound
```
Finally, add a model, with a ```CurrentPage```
and a ```CurrentUrl```

```fsharp
type Model = { CurrentPage: Page; CurrentUrl: Url }
```
#### 5. Adding new messages to the Index
The Index page needs to deal with messages that indicate Url changes, but it also needs to pass messages to the TodoList update method. Create a ```Msg``` type with a case of ```Url``` for ```UrlChanged``` and a case of ```TodoList.Msg``` for ```TodoListMsg```
```fsharp
type Msg =
    | UrlChanged of Url
    | TodolistMsg of TodoList.Msg
```
#### 6. Initialization
Whenever your application is started, or when a user navigates to another page, we need to run initialization based on the url.

##### Initialization based on an Url

Create a function intiFormUrl, that returns a model and command for a given Url.

If the Url is for the TodoList, use ```TodoList.initialize``` to initialize the TodoList. Create a new model with as ```CurrentPage``` a ```TodoList``` page of the initialized todo list model, and as ```currentUrl``` ```Url.TodoList```. Return both the new model, and the command returned by the initialization, mapping it to a TodoListMessage.
```fsharp
let initFromUrl url =
    match url with
    | Url.TodoList ->
        let todoListModel, todoListMsg = TodoList.init ()

        let model =
            { CurrentPage = TodoList todoListModel
              CurrentUrl = url }

        model, todoListMsg |> Cmd.map TodolistMsg
```

for the ```NotFound``` Url, return a model with ```NotFound``` as the ```CurrentPage``` and for the ```CurrentUrl``` ```Url.NotFound```.

```fsharp
match url with
...
| Url.NotFound ->
    { CurrentPage = NotFound
      CurrentUrl = Url.NotFound },
    Cmd.none
```
##### Elmish initialization
To allow Elmish to initialize the page, you also need the init function again; it should return a ```Model``` and ```Command```. Retrieve the route segments using ```Router.CurrentPath```, parse it into an ```Url```, and use ```initFormUrl``` to create your ```Model``` and ```Command```

```fsharp
let init () : Model * Cmd<Msg> =
    Router.currentPath ()
    |> parseUrl
    |> initFromUrl
```

#### 4. Adding the update function to the Index
Add a function update to the index with as signature ```(model: Model) -> (message: Msg) -> Model * Command```.

Inside the update function, pattern match on the current page of the model and the message.

Include a match for all UrlChanged messages, regardless of the current page; let this call initFromUrl with the url from the message

```fsharp
let update (message: Msg) (model: Model) : Model * Cmd<Msg> =
    | _, UrlChanged url -> initFromUrl url
```

##### Updating the TodoList model
When matching with the ```TodoList``` page and a ```TodoListMsg```, update the TodoListModel with the ```update``` function of the TodoList Module. Then create a new Model with as ```CurrentPage``` the ```TodoList``` with the new TodoModel. Finally, return the model and command returned by the todolist update, mapping the command to a TodoListMsg

```fsharp
let update (message: Msg) (model: Model) : Model * Cmd<Msg> =
    match model.CurrentPage, message with
    | TodoList todolist, TodolistMsg todolistMessage ->
        let newTodoListModel, newCommand = TodoList.update todolistMessage todolist
        let model = { model with CurrentPage = TodoList newTodoListModel }
        model, newCommand |> Cmd.map TodolistMsg
    | _, UrlChanged url -> initFromUrl url
```

#### Rendering the right page
In order to render the right content on the page, re-introduce the containerBox that we moved to the TodoList page earlier. This function should return page content based on ```model.Page```. For the ```NotFound``` page, return a box with the text "Page not found".

For the TodoList page, call ```TodoList.view```, and pass it the todo model. you also need to pass it the dispatch function; the dispatch function from the TodoList is supposed to be a function of ```Todolist.Msg -> unit```, but the index view receives a dispatch function of ```Index.Msg -> unit```, so wrap the message in a ```TodoListMsg``` first.

```fsharp
let containerBox model dispatch =
    match model.CurrentPage with
    | TodoList todoModel -> TodoList.view todoModel ( TodolistMsg>>dispatch )
    | NotFound -> Bulma.box "Page not found"

```
The routing should work now. Try navigating to [localhost:8080](http://localhost:8080/todo); you should see a page with "Page not Found". If you go to [localhost:8080/todo](http://localhost:8080/todo), you should see the todo app.