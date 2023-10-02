# How do I create multi-page applications with routing and the useElmish hook?

If you build an application with multiple pages that have their own states that do not interact, you can use the useElmish hook to create pages with their own Model View Update architecture.

## 1. Installing dependencies

Install Feliz.Router in the Client project

```bash
dotnet paket add Feliz.Router -p Client
```

Install Feliz.UseElmish in the Client project

```bash
dotnet paket add Feliz.UseElmish -p client
```

Open the router in the client project

```
open Feliz.Router
```

## 2. Extracting the todo list module

Create a new Module `TodoList` in the client project. Move the following functions and types to the TodoList Module:

* Model
* Msg
* todosApi
* init
* update
* containerBox; rename this to view

```fsharp title="TodoList.fs"
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

## 4. Add the UseElmish hook to the TodoList Module

open Feliz.UseElmish in the TodoList Module

```fsharp title="TodoList.fs"
open Feliz.UseElmish
...
```

On the first line of the TodoList's view function, call `React.useElmish`, passing it the `init` and `update` functions. Bind the output to `model` and `dispatch`

=== "Code"
    ```fsharp title="TodoList.fs"
    let view (model: Model) (dispatch: Msg -> unit) =
        let model, dispatch = React.useElmish( init, update, [||] )
        ...
    ```

=== "Diff"
    ```.diff title="TodoList.fs"
    let view (model: Model) (dispatch: Msg -> unit) =
    +   let model, dispatch = React.useElmish( init, update, [||] )
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

Create a model that holds the current page

```fsharp title="Index.fs"
Type Page =
    | TodoList
    | NotFound

Type Model =
    { CurrentPage: Page }
```

## 5. Add a function to parse Urls

add an `Url` type. Use the RequireQualifiedAccess to avoid clashes with `Page`

```fsharp title="Index.fs"
Type Url =
    | TodoList
    | NotFound
```

Create a function that parses a Feliz.Router's list of Url segments routes to a `Url`

```fsharp title="Index.fs"
let ParseUrl url = 
    match url with 
    | [ "Todo" ] -> TodoList
    | _ -> notFound
```

## 6. Initialize the application

Create a function that initializes the app based on an url

```fsharp title="Index.fs"
let initFromUrl url =
    match url with
    | Url.TodoList ->
        let model = { CurrentPage = TodoList }
        model, Cmd.none
    | Url.NotFound ->
        let model = { CurrentPage = NotFound }
        model, Cmd.none
```

Create a new `init` function, that fetches the current url, and calls initFromUrl

```fsharp title="Index.fs"
let init () : Model * Cmd<Msg> =
    Router.currentPath ()
    |> parseUrl
    |> initFromUrl
```
## 7. Updating the Url

Add a `Msg` type, with an UrlChanged case

```fsharp title="Index.fs"
type Msg = 
    | UrlChanged of Url
```
add an update method, that reinitializes the application when the Url changes

```fsharp title="Index.fs"
let update (msg: Msg) (model: Model) : Model * Cmd<Msg> =
    match msg with
    | UrlChanged url ->
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
        React.router[
            router.onUrlChanged ( parseUrl>>UrlChanged>>dispatch )
            router.children[
                Bulma.hero[
                ...
                ]
            ]
        ]
    ```
=== "Diff"
    ```diff title="Index.fs"
    let view (model: Model) (dispatch: Msg -> unit) =
    +   React.router[
    +       router.onUrlChanged ( parseUrl>>UrlChanged>>dispatch )
    +       router.children[
                Bulma.hero[
                ...
                ]
    +       ]
    +   ]
    ```

## 10.  Try it out

The routing should work now. Try navigating to [localhost:8080](http://localhost:8080/); you should see a page with "Page not Found". If you go to [localhost:8080/todo](http://localhost:8080/todo), you should see the todo app.
