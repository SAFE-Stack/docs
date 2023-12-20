# How do I add routing to a SAFE app with separate model for every page?

*Written for SAFE template version 4.2.0*

If your application has multiple separate components, there is no need to have one big, complex model that manages all the state for all components. In this recipe we separate the information of the todo list out of the main `Model`, and give the todo list application its own route. We also add a "Page not found" page.

## 1. Adding the Feliz router

Install Feliz.Router in the client project

```bash
dotnet paket add Feliz.Router -p Client
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
* toDoAction
* todoList; rename this to view and remove the `private` access modifier

also open `Shared`, `Fable.Remoting.Client`, `Elmish` and `Feliz`

```fsharp title="TodoList.fs"
module TodoList

open Shared
open Fable.Remoting.Client
open Elmish
open Feliz

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

let init () =
    let model = { Todos = []; Input = "" }
    let cmd = Cmd.OfAsync.perform todosApi.getTodos () GotTodos
    model, cmd

let update msg model =
    match msg with
    | GotTodos todos -> { model with Todos = todos }, Cmd.none
    | SetInput value -> { model with Input = value }, Cmd.none
    | AddTodo ->
        let todo = Todo.create model.Input

        let cmd = Cmd.OfAsync.perform todosApi.addTodo todo AddedTodo

        { model with Input = "" }, cmd
    | AddedTodo todo ->
        {
            model with
                Todos = model.Todos @ [ todo ]
        },
        Cmd.none

let private todoAction model dispatch =
    Html.div [
        prop.className "flex flex-col sm:flex-row mt-4 gap-4"
        prop.children [
            Html.input [
                prop.className
                    "shadow appearance-none border rounded w-full py-2 px-3 outline-none focus:ring-2 ring-teal-300 text-grey-darker"
                prop.value model.Input
                prop.placeholder "What needs to be done?"
                prop.autoFocus true
                prop.onChange (SetInput >> dispatch)
                prop.onKeyPress (fun ev ->
                    if ev.key = "Enter" then
                        dispatch AddTodo)
            ]
            Html.button [
                prop.className
                    "flex-no-shrink p-2 px-12 rounded bg-teal-600 outline-none focus:ring-2 ring-teal-300 font-bold text-white hover:bg-teal disabled:opacity-30 disabled:cursor-not-allowed"
                prop.disabled (Todo.isValid model.Input |> not)
                prop.onClick (fun _ -> dispatch AddTodo)
                prop.text "Add"
            ]
        ]
    ]

let view model dispatch =
    Html.div [
        prop.className "bg-white/80 rounded-md shadow-md p-4 w-5/6 lg:w-3/4 lg:max-w-2xl"
        prop.children [
            Html.ol [
                prop.className "list-decimal ml-6"
                prop.children [
                    for todo in model.Todos do
                        Html.li [ prop.className "my-1"; prop.text todo.Description ]
                ]
            ]

            todoAction model dispatch
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
let init () =
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
    let update message model =
        ...
        match model.CurrentPage, message with
        | _, UrlChanged url -> initFromUrl url
    ```
=== "Diff"
    ```diff title="Index.fs"
     let update message model =
         ...
    +    match model.CurrentPage, message with
    +    | _, UrlChanged url -> initFromUrl url
    ```

## 8. Catching all cases in the update function

Complete the pattern match in the `update` function, adding a case with a wildcard for both `message` and `model`. Return the model, and no command

=== "Code"
    ```fsharp title="Index.fs"
    let update message model =
        ...
        | _, _ -> model, Cmd.none
    ```
=== "Diff"
    ```.diff title="Index.fs"
     let update message model =
         ...
    +    | _, _ -> model, Cmd.none
    ```
## 9. Rendering pages

Add a function pageContent to the `Index` module. If the CurrentPage is of `TodoList`, render the todo list using `TodoList.view`; in order to dispatch a `TodoList.Msg`, it needs to be wrapped in a `TodoListMsg`.

For the `NotFound` page, return a "Page not found" box

```fsharp title="Index.fs"
let pageContent model dispatch =
    match model.CurrentPage with
    | TodoList todoListModel -> TodoList.view todoListModel (TodoListMsg >> dispatch)
    | NotFound -> Html.text "Page not found"
```

In the view function, replace the call to `todoList` with a call to `pageContent`

=== "Code"
    ```fsharp title="Index.fs"
    let view model dispatch =
        ...
        pageContent model dispatch
        ...
    ```
=== "Diff"
    ```.diff title="Index.fs"
     let view model dispatch =
         ...
    +     pageContent model dispatch
    -     pageContent model dispatch
         ...
    ```

## 10. Adding the React router to the view

Wrap the content of the view function in a `router.children` property of a `React.router`. Also add an `onUrlChanged` property, that dispatches the 'UrlChanged' message.

=== "Code"
    ```fsharp title="Index.fs"
    let view model dispatch =
        React.router [
            router.onUrlChanged (UrlChanged >> dispatch)
            router.children [
                Html.section [
                ...
                ]
            ]
        ]
    ```
=== "Diff"
    ```.diff title="Index.fs"
     let view model dispatch =
    +    React.router [
    +        router.onUrlChanged (UrlChanged >> dispatch)
    +        router.children [
                 Html.section [
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