# How do I create multi-page applications with routing and the useElmish hook?

*Written for SAFE template version 4.2.0*

If you build an application with multiple pages that have their own states that do not interact, you can use the useElmish hook to create pages with their own Model View Update architecture.

## 1. Installing dependencies

!!! warning "Pin Feliz.Core to V3"
    At the time of writing, the published version of the SAFE template does not have the version of `Feliz.Core` pinned; this can create problems when installing dependencies.

    If you are using version v.4.2.0 of the template, pin `Fable.Core` to version 3 in `paket.depedencies` at the root of the project

    ```.diff title="paket.dependencies"
    ...
    -nuget Fable.Core
    +nuget Fable.Core ~> 3
    ...
    ```


Install Feliz.Router in the Client project

```bash
dotnet paket add Feliz.Router -p Client -V 3.8
```

Install Feliz.UseElmish in the Client project

```bash
dotnet paket add Feliz.UseElmish -p client
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
* update
* containerBox

Also open `Shared`, `Fable.Remoting.Client`, `Elmish`, `Feliz.Bulma` and `Feliz`. 

```fsharp title="TodoList.fs"
module TodoList

open Shared
open Fable.Remoting.Client
open Elmish

open Feliz.Bulma
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

## 4. Add the UseElmish hook to the TodoList Module

open Feliz.UseElmish in the TodoList Module

```fsharp title="TodoList.fs"
open Feliz.UseElmish
...
```

In the todoList module, rename `containerBox` to view.
On the first line, call `React.useElmish` passing it the `init` and `update` functions. Bind the output to `model` and `dispatch`

=== "Code"
    ```fsharp title="TodoList.fs"
    let view (model: Model) (dispatch: Msg -> unit) =
        let model, dispatch = React.useElmish( init, update, [||] )
        ...
    ```

=== "Diff"
    ```.diff title="TodoList.fs"
    -let containerBox (model: Model) (dispatch: Msg -> unit) =
    +let view (model: Model) (dispatch: Msg -> unit) =
    +    let model, dispatch = React.useElmish( init, update, [||] )
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
    - let view (model: Model) (dispatch: Msg -> unit) =
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
    | ["todo"] ->
        let model = {CurrentPage = TodoList }

        model, Cmd.none
    | _ ->
        let model = { CurrentPage = NotFound }

        model, Cmd.none
```

Create a new `init` function, that fetches the current url, and calls initFromUrl. 

```fsharp title="Index.fs"
let init () =
    Router.currentUrl ()
    |> initFromUrl
```
## 7. Updating the Page

Add a `Msg` type, with an PageChanged case

```fsharp title="Index.fs"
type Msg = 
    | PageChanged of string list
```
add an update method, that reinitializes the application when the URL changes

```fsharp title="Index.fs"
let update (msg: Msg) (model: Model) : Model * Cmd<Msg> =
    match msg with
    | PageChanged url ->
        initFromUrl url
```

## 8. Displaying pages

Add a containerBox function to the `Index module`, that returns the appropriate page content

```fsharp title="Index.fs"
let containerBox (model: Model) (dispatch: Msg -> unit) =
    match model.CurrentPage with
    | NotFound -> Bulma.box "Page not found"
    | TodoList -> TodoList.view ()
```
## 9. Add the router to the view

Wrap the content of the view method in a `React.Router` element's router.children property, and add a `router.onUrlChanged` property to dispatch the urlChanged message

=== "Code"
    ```fsharp title="Index.fs"
    let view (model: Model) (dispatch: Msg -> unit) =
        React.router [
            router.onUrlChanged ( PageChanged>>dispatch )
            router.children [
                Bulma.hero [
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
                Bulma.hero [
                ...
                ]
    +       ]
    +   ]
    ```

## 10.  Try it out

The routing should work now. Try navigating to [localhost:8080](http://localhost:8080/); you should see a page with "Page not Found". If you go to [localhost:8080/#/todo](http://localhost:8080/#/todo), you should see the todo app.
