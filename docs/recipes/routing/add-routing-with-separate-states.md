# How do I include multiple pages with their own state?
If your application has multiple separate components, there is no need to have one big, complex state that manages all the information for all components. In this recipe we separate the information of the todo list out of main ```Model```, and give it it's own route. We also add a "Page not found" page.

## 1. Install Feliz router

```bash
dotnet paket add Feliz.Router -p Client
```
To include the router in the Client, add `open Feliz.Router` at the top of index.fs.

## 3. Creating a module for the Todo list
Move the following functions and types to the TodoList Module:
* Model
* Msg
* todosApi
* init
* update
* containerBox; rename this to view

```fsharp
module TodoList

open Shared
open Fable.Remoting.Client
open Elmish

type Model = { Todos: Todo list; Input: string }

type Msg =
    | GotTodos of Todo list
    | SetInput of string
    | AddTodo
    | AddedTodo of Todo

let todosApi =
    Remoting.createApi ()
    |> Remoting.withRouteBuilder Route.builder
    |> Remoting.buildProxy<ITodosApi>

let init () : Model * Cmd<Msg> =
    let model = { Todos = []; Input = "" }
    let cmd = Cmd.OfAsync.perform todosApi.getTodos () GotTodos

    model, cmd

let update (msg: Msg) (model: Model) : Model * Cmd<Msg> =
    match msg with
    | GotTodos todos -> { model with Todos = todos }, Cmd.none
    | SetInput value -> { model with Input = value }, Cmd.none
    | AddTodo ->
        let todo = Todo.create model.Input

        let cmd = Cmd.OfAsync.perform todosApi.addTodo todo AddedTodo

        { model with Input = "" }, cmd
    | AddedTodo todo -> { model with Todos = model.Todos @ [ todo ] }, Cmd.none

open Feliz.Bulma
open Feliz

let view (model: Model) (dispatch: Msg -> unit) =
    Bulma.box [
        Bulma.content [
            Html.ol [
                for todo in model.Todos do
                    Html.li [ prop.text todo.Description ]
            ]
        ]
        Bulma.field.div [
            field.isGrouped
            prop.children [
                Bulma.control.p [
                    control.isExpanded
                    prop.children [
                        Bulma.input.text [
                            prop.value model.Input
                            prop.placeholder "What needs to be done?"
                            prop.onChange (fun x -> SetInput x |> dispatch)
                        ]
                    ]
                ]
                Bulma.control.p [
                    Bulma.button.a [
                        color.isPrimary
                        prop.disabled (Todo.isValid model.Input |> not)
                        prop.onClick (fun _ -> dispatch AddTodo)
                        prop.text "Add"
                    ]
                ]
            ]
        ]
    ]
```


## 4. Adding a new Model to the Index page

Create a new Model to the `Index` module, to keep track of the open page and Url

```fsharp
type Page =
    | TodoList of TodoList.Model
    | NotFound 

type Url =
    | TodoList
    | NotFound
    
type Model = { CurrentPage: Page; CurrentUrl: Url }

```


## 2.  Parsing URLs

Add a function to parse `Feliz.Router` style Urls to the `Url` type.

```fsharp
let parseUrl url = 
    match url with
    | ["todo"] -> Url.TodoList
    | _ -> Url.NotFound
```

## Updating the TodoList model

Add a `Msg` type with a case of `TodoList.Msg`

```fsharp
type Msg =
    ...
    | TodoListMsg of TodoList.Msg
```

Create an `update` function (we moved the original one to `TodoList`). Handle the `TodoListMsg` by updating the `TodoList` Model

```fsharp
let update (message: Msg) (model: Model) : Model * Cmd<Msg> =
    match model.CurrentPage, message with
    | TodoList todolist, TodolistMsg todolistMessage ->
        let newTodoListModel, newCommand = TodoList.update todolistMessage todolist
        let model = { model with CurrentPage = TodoList newTodoListModel }
        model, newCommand |> Cmd.map TodolistMsg
    ...
```

## 6. Initializing from Url

Create a function initFormUrl; return a model with a TodoList `CurrentPage` and `CurrentUrl` when given a `TodoList` Url. Also return the command that TodoLists `init` may return, wrapped in a `TodoListMsg`

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

Also add a case for the NotFound page

```fsharp
let initFromUrl url =
    match url with
    ...
    | Url.NotFound ->
        let model =
            { CurrentPage = NotFound
              CurrentUrl = url }

        model, todoListMsg |> Cmd.map TodolistMsg
```
## Elmish initialization

Add an init function to `Index`; return the current page based on `Router.currentPath`

```fsharp
let init () : Model * Cmd<Msg> =
    Router.currentPath ()
    |> parseUrl
    |> initFromUrl
```


## 5. Handling Url Changes

Add an `UrlChanged` case to the `Msg` type

```fsharp
type Msg =
    ...
    | UrlChanged of Url
```

Handle the case in the `update` function by initializing the appropriate model

```fsharp
let update (message: Msg) (model: Model) : Model * Cmd<Msg> =
    match model.CurrentPage, message with
    | _, UrlChanged url -> initFromUrl url
```



#### Rendering the right page

Add a function containerBox. Retur

```fsharp
let containerBox model dispatch =
    match model.CurrentPage with
    | TodoList todoModel -> TodoList.view todoModel ( TodolistMsg>>dispatch )
    | NotFound -> Bulma.box "Page not found"

```
The routing should work now. Try navigating to [localhost:8080](http://localhost:8080/todo); you should see a page with "Page not Found". If you go to [localhost:8080/todo](http://localhost:8080/todo), you should see the todo app.