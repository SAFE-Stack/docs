# How do I add routing to a SAFE app with separate model for every page?

*Written for SAFE template version 4.2.0*

If your application has multiple separate components, there is no need to have one big, complex model that manages all the state for all components. In this recipe we separate the information of the todo list out of the main `Model`, and give the todo list application its own route. We also add a "Page not found" page.

## 1. Adding the Feliz router

Install Feliz.Router in the client project

```bash
dotnet paket add Feliz.Router -p Client -V 3.8
```

!!! Warning "Feliz.Router versions"
    At the time of writing, the current version of the SAFE template (4.2.0) does not work well with the latest version of Feliz.Router (4.0).
    To work around this, we install Feliz.Router 3.8, the latest version that works with SAFE template version 4.2.0.

    If you are working with a newer version of the SAFE template, it might be worth trying to install the newest version of Feliz.Router.
    To see the installed version of the SAFE template, run in the command line:
    
    ```bash
    dotnet new --list
    ```

To include the router in the Client, open `Feliz.Router` at the top of Index.fs

```fsharp title="Index.fs"
open Feliz.Router
```

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

Create a new Model in the `Index` module, to keep track of the open page

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
    | TodoListMsg of TodoList.Msg
```

Create an `update` function (we moved the original one to `TodoList`). Handle the `TodoListMsg` by updating the `TodoList` Model. Wrap the command returned by the `update` of the todo list in a `TodoListMsg` before returning it. We expand this function later with other cases that deal with navigation.

```fsharp title="Index.fs"
let update (message: Msg) (model: Model) : Model * Cmd<Msg> =
    match model.CurrentPage, message with
    | TodoList todoList, TodoListMsg todoListMessage ->
        let newTodoListModel, newCommand = TodoList.update todoListMessage todoList
        let model = { model with CurrentPage = TodoList newTodoListModel }
        
        model, newCommand |> Cmd.map TodoListMsg
```

## 5. Initializing from URL

Create a function `initFromUrl`; initialize the `TodoList` app when given the URL of the todo list app. Also return the command that TodoList's `init` may return, wrapped in a `TodoListMsg`

```fsharp title="Index.fs"
let initFromUrl url =
    match url with
    | [ "todo" ] ->
        let todoListModel, todoListMsg = TodoList.init ()
        let model = { CurrentPage = TodoList todoListModel }

        model, todoListMsg |> Cmd.map TodoListMsg
```

Add a wildcard, so any URLs that are not registered display the "not found" page

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
         ...
    +    | _ -> { CurrentPage = NotFound }, Cmd.none
    ```

## 6. Elmish initialization

Add an `init` function to `Index`; return the current page based on `Router.currentUrl`

```fsharp title="Index.fs"
let init () : Model * Cmd<Msg> =
    Router.currentUrl ()
    |> initFromUrl
```

## 7. Handling URL Changes

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

Handle the case in the `update` function by calling `initFromUrl`

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

Complete the pattern match in the `update` function, adding a case with a wildcard for both `message` and `model`. Return the model, and no command

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

Add a function containerBox to the `Index` module. If the CurrentPage is of `TodoList`, render the todo list using `TodoList.view`; in order to dispatch a `TodoList.Msg`, it needs to be wrapped in a `TodoListMsg`.

For the `NotFound` page, return a "Page not found" box

```fsharp title="Index.fs"
let containerBox model dispatch =
    match model.CurrentPage with
    | TodoList todoModel -> TodoList.view todoModel (TodoListMsg >> dispatch)
    | NotFound -> Bulma.box "Page not found"
```

## 10. Adding the React router to the view

Wrap the content of the view function in a `router.children` property of a `React.router`. Also add an `onUrlChanged` property, that dispatches the 'UrlChanged' message.

=== "Code"
    ```fsharp title="Index.fs"
    let view (model: Model) (dispatch: Msg -> unit) =
        React.router [
            router.onUrlChanged (UrlChanged >> dispatch)
            router.children [
                Bulma.hero [
                ...
                ]
            ]
        ]
    ```
=== "Diff"
    ```.diff title="Index.fs"
     let view (model: Model) (dispatch: Msg -> unit) =
    +    React.router [
    +        router.onUrlChanged (UrlChanged >> dispatch)
    +        router.children [
                 Bulma.hero [
                 ...
                 ]
    +        ]
    +    ]
    ```

## 11. Running the app

The routing should work now. Try navigating to [localhost:8080](http://localhost:8080/todo); you should see a page with "Page not Found". If you go to [localhost:8080/#/todo](http://localhost:8080/#/todo), you should see the todo app.

!!! info "# sign"
    You might be surprised to see the hash sign as part of the URL. It enables React to react to URL changes without a full page refresh.
    There are ways to omit this, but getting this to work properly is outside of the scope of this recipe.