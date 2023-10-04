# How do I include multiple pages with their own state?
If your application has multiple separate components, there is no need to have one big, complex state that manages all the information for all components. In this recipe we separate the information of the todo list out of main ```Model```, and give it it's own route. We also add a "Page not found" page.

## 1. Installing Feliz router

```bash
dotnet paket add Feliz.Router -p Client -V 3.8
```
To include the router in the Client, add `open Feliz.Router` at the top of index.fs.

## 2. Creating a module for the Todo list
Move the following functions and types to a new `TodoList` Module in a file `TodoList.fs`:
* Model
* Msg
* todosApi
* init
* update
* containerBox; rename this to view

also open `Shared`, `Fable.Remoting.Client`, `Elmish` `Feliz` and `Feliz.Bulma`

```fsharp title="TodoList.fs"
module TodoList

open Shared
open Fable.Remoting.Client
open Elmish
open Feliz
open Feliz.Bulma


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


## 3. Adding a new Model to the Index page

Create a new Model to the `Index` module, to keep track of the open page.

```fsharp title="Index.fs"
type Page =
    | TodoList of TodoList.Model
    | NotFound 

type Model = { CurrentPage: Page }
```

## 4. Updating the TodoList model

Add a `Msg` type with a case of `TodoList.Msg`

```fsharp title="Index.fs"
type Msg =
    | TodolistMsg of TodoList.Msg
```

Create an `update` function (we moved the original one to `TodoList`). Handle the `TodoListMsg` by updating the `TodoList` Model

```fsharp title="Index.fs"
let update (message: Msg) (model: Model) : Model * Cmd<Msg> =
    match model.CurrentPage, message with
    | TodoList todolist, TodolistMsg todolistMessage ->
        let newTodoListModel, newCommand = TodoList.update todolistMessage todolist
        let model = { model with CurrentPage = TodoList newTodoListModel }
        model, newCommand |> Cmd.map TodolistMsg
```

## 5. Initializing from Url

Create a function initFormUrl; initialize the `TodoList` app when given the url of the todo list app. Also return the command that TodoLists `init` may return, wrapped in a `TodoListMsg`

```fsharp title="Index.fs"
let initFromUrl url =
    match url with
    | [ "todo" ] ->
        let todoListModel, todoListMsg = TodoList.init ()
        let model = { CurrentPage = TodoList todoListModel }

        model, todoListMsg |> Cmd.map TodolistMsg
```

Add a wildcard, so any urls that are not registered display the "not found" page

=== "Code"
    ```fsharp title="Index.fs"
    let initFromUrl url =
        match url with
        ...
        | _ -> { CurrentPage = NotFound }, Cmd.none
    ```
=== "Diff"
    ```.diff title="Index.fs"
     let initFromUrl url =
         match url with
    +    | _ -> { CurrentPage = NotFound }, Cmd.none
    ```

## 6. Elmish initialization

Add an init function to `Index`; return the current page based on `Router.currentPath`

```fsharp title="Index.fs"
let init () : Model * Cmd<Msg> =
    Router.currentUrl ()
    |> initFromUrl
```


## 7. Handling Url Changes

Add an `UrlChanged` case of `string list` to the `Msg` type

=== "Code"
    ```fsharp title="Index.fs"
    type Msg =
        ...
        | UrlChanged of string list
    ```
=== "Diff"
    ```diff title="Index.fs"
     type Msg =
         ...
    +    | UrlChanged of string list
    ```

Handle the case in the `update` function by calling initFromUrl

=== "Code"
    ```fsharp title="Index.fs"
    let update (message: Msg) (model: Model) : Model * Cmd<Msg> =
        ...
        match model.CurrentPage, message with
        | _, UrlChanged url -> initFromUrl url
    ```
=== "Diff"
    ```diff title="Index.fs"
     let update (message: Msg) (model: Model) : Model * Cmd<Msg> =
         ...
    +    match model.CurrentPage, message with
    +    | _, UrlChanged url -> initFromUrl url
    ```

## 8. Catching all cases in the update function

Complete the pattern match in the update method, adding a case with a wildcard for both model and message. Return the model, and no command.

=== "Code"
    ```fsharp title="Index.fs"
    let update (message: Msg) (model: Model) : Model * Cmd<Msg> =
        ...
        | _, _ -> model, Cmd.none
    ```
=== "Diff"
    ```.diff title="Index.fs"
     let update (message: Msg) (model: Model) : Model * Cmd<Msg> =
         ...
    +    | _, _ -> model, Cmd.none
    ```
## 9. Rendering pages

Add a function containerBox to the `Index` module. If the CurrentPage is of `TodoList`, render the todo list using `TodoList.view`; in order to dispatch a `TodoList.Msg`, it needs to be wrapped in a `TodoListMsg`

For the `NotFound` page, return a "Page not found" message.

```fsharp title="Index.fs"
let containerBox model dispatch =
    match model.CurrentPage with
    | TodoList todoModel -> TodoList.view todoModel ( TodolistMsg>>dispatch )
    | NotFound -> Bulma.box "Page not found"
```

## 10. Running the app

The routing should work now. Try navigating to [localhost:8080](http://localhost:8080/todo); you should see a page with "Page not Found". If you go to [localhost:8080/todo](http://localhost:8080/todo), you should see the todo app.