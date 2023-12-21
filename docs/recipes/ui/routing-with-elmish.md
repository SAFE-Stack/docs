# How do I create multi-page applications with routing and the useElmish hook?

*Written for SAFE template version 4.2.0*

[UseElmish](https://zaid-ajaj.github.io/Feliz/#/Hooks/UseElmish) is a powerful package that allows you to write standalone components using Elmish. A component built around the `UseElmish` hook has its own view, state and update function.

In this recipe we add routing to a safe app, and implement the todo list page using the `UseElmish` hook.

## 1. Installing dependencies

Install Feliz.Router in the Client project

```bash
dotnet paket add Feliz.Router -p Client
```

Install Feliz.UseElmish in the Client project

```bash
cd src/Client
dotnet femto install Feliz.UseElmish
```

Open the router in the client project

```fsharp title="Index.fs"
open Feliz.Router
```

## 2. Extracting the todo list module

Create a new Module `TodoList` in the client project. Move the following functions and types to the TodoList Module:

* Model
* Msg
* todosApi
* init
* todoAction
* todoList

Also open `Shared`, `Fable.Remoting.Client`, `Elmish` and `Feliz`. 

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

[<ReactComponent>]
let todoList model dispatch =
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

## 4. Add the UseElmish hook to the TodoList view function

open Feliz.UseElmish in the TodoList Module

```fsharp title="TodoList.fs"
open Feliz.UseElmish
...
```

In the todoList module, rename the function `todoList` to `view`, and remove the `private` access modifier.
On the first line, call `React.useElmish`, passing it the `init` and `update` functions. Bind the output to `model` and `dispatch`

=== "Code"
    ```fsharp title="TodoList.fs"
    let view model dispatch =
        let model, dispatch = React.useElmish (init, update, [||])
        ...
    ```

=== "Diff"
    ```.diff title="TodoList.fs"
    -let containerBox model dispatch =
    +let view model dispatch =
    +    let model, dispatch = React.useElmish (init, update, [||])
        ...
    ```

Replace the arguments of the function with unit, and add the `ReactComponent` attribute to it

=== "Code"
    ```fsharp title="Index.fs"
    [<ReactComponent>]
    let view () =
        ...
    ```
=== "Diff"
    ```.diff title="Index.fs"
    + [<ReactComponent>]
    - let view model dispatch =
    + let view () =
          ...
    ```

## 5. Add a new model to the Index module

In the `Index module`, create a model that holds the current page

```fsharp title="Index.fs"
type Page =
    | TodoList
    | NotFound

type Model =
    { CurrentPage: Page }
```
## 6. Initializing the application

Create a function that initializes the app based on an url

```fsharp title="Index.fs"
let initFromUrl url =
    match url with
    | [ "todo" ] ->
        let model = { CurrentPage = TodoList }

        model, Cmd.none
    | _ ->
        let model = { CurrentPage = NotFound }

        model, Cmd.none
```

Create a new `init` function, that fetches the current url, and calls initFromUrl. 

```fsharp title="Index.fs"
let init () = Router.currentUrl () |> initFromUrl
```
## 7. Updating the Page

Add a `Msg` type, with an PageChanged case

```fsharp title="Index.fs"
type Msg = 
    | PageChanged of string list
```
Add an `update` function, that reinitializes the app based on an URL

```fsharp title="Index.fs"
let update msg model =
    match msg with
    | PageChanged url -> initFromUrl url
```

## 8. Displaying pages

Add a pageContent function to the `Index` module, that returns the appropriate page content

```fsharp title="Index.fs"
let pageContent model =
    match model.CurrentPage with
    | NotFound -> Html.text "Page not found"
    | TodoList -> TodoList.view ()
```

In the `view` function, replace the call to `todoList` with a call to `pageContent`

=== "Code"
    ```
    let view model dispatch =
        Html.section [
            ...
            pageContent model
            ...
        ]
    ```
=== "Diff"
```
     let view model dispatch =
         Html.section [
         ...
     -   todoList view model
     +   pageContent model
         ...
         ]
```

## 9. Add the router to the view

Wrap the content of the view method in a `React.Router` element's router.children property, and add a `router.onUrlChanged` property to dispatch the urlChanged message

=== "Code"
    ```fsharp title="Index.fs"
    let view model dispatch =
        React.router [
            router.onUrlChanged ( PageChanged>>dispatch )
            router.children [
                Html.section [
                ...
                ]
            ]
        ]
    ```
=== "Diff"
    ```diff title="Index.fs"
    let view (model: Model) (dispatch: Msg -> unit) =
    +   React.router [
    +       router.onUrlChanged ( PageChanged>>dispatch )
    +       router.children [
                Html.section [
                ...
                ]
    +       ]
    +   ]
    ```

## 10.  Try it out

The routing should work now. Try navigating to [localhost:8080](http://localhost:8080/); you should see a page with "Page not Found". If you go to [localhost:8080/#/todo](http://localhost:8080/#/todo), you should see the todo app.

!!! info "# sign"
    You might be surprised to see the hash sign as part of the URL. It enables React to react to URL changes without a full page refresh.
    There are ways to omit this, but getting this to work properly is outside of the scope of this recipe.