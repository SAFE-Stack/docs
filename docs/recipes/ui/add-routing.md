# How do I add routes to a SAFE app without splitting the app into multiple models?

When building larger apps, you probably want different pages to be accessible through different URLs. In this recipe, we show you how to add routes to different pages to an application, including adding a "page not found" page that is displayed when an unknown URL is entered.

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

To include the router in the Client, add `open Feliz.Router` at the top of Index.fs

## 2. Adding the URL object

Add the current page to the model of the client, using a new `Page` type

=== "Code"
    ```fsharp
    type Page =
        | TodoList
        | NotFound
    
    type Model =
        { CurrentPage: Page
          Todos: Todo list
          Input: string }
    ```
=== "Diff"
    ```.diff
    + type Page =
    +     | TodoList
    +     | NotFound
    +
    - type Model = { Todos: Todo list; Input: string }
    + type Model =
    +    { CurrentPage: Page
    +      Todos: Todo list
    +      Input: string }
    ```

## 3.  Parsing URLs

Create a function to parse a URL to a page, including a wildcard for unmapped pages

```fsharp
let parseUrl url = 
    match url with
    | ["todo"] -> Page.TodoList
    | _ -> Page.NotFound
```

## 4. Initialization when using a URL

On initialization, set the current page

=== "Code"
    ```fsharp 
    let init () : Model * Cmd<Msg> =
        let page = Router.currentUrl () |> parseUrl
    
        let model =
            { CurrentPage = page
              Todos = []
              Input = "" }
        ...
        model, cmd
    ```
=== "Diff"
    ```diff
      let init () : Model * Cmd<Msg> =
    +     let page = Router.currentUrl () |> parseUrl
    +
    -      let model = { Todos = []; Input = "" }
    +      let model =
    +        { CurrentPage = page
    +         Todos = []
    +         Input = "" }
          ...
          model, cmd
    ```
## 5. Updating the URL

Add an action to handle navigation.

To the `Msg` type, add a `PageChanged` case of `Page`

=== "Code"
    ```fsharp
    type Msg =
        ...
        | PageChanged of Page
    ```
=== "diff"
    ```.diff 
     type Msg =
         ...
    +    | PageChanged of Page
    ```

Add the `PageChanged` update action

=== "Code"
    ```fsharp
    let update (msg: Msg) (model: Model) : Model * Cmd<Msg> =
        match msg with
        ...
        | PageChanged page -> { model with CurrentPage = page }, Cmd.none
    ```
=== "Diff"
    ```.diff
      let update (msg: Msg) (model: Model) : Model * Cmd<Msg> =
          match msg with
          ...
    +     | PageChanged page -> { model with CurrentPage = page }, Cmd.none
    ```

## 6. Displaying the correct content

Rename the `view` function to `todoView`

=== "Code"
    ```fsharp
    let todoView (model: Model) (dispatch: Msg -> unit) =
        Bulma.hero [
        ...
        ]
    ```
=== "Diff"
    ```.diff
    - let view (model: Model) (dispatch: Msg -> unit) =
    + let todoView (model: Model) (dispatch: Msg -> unit) =
          Bulma.hero [
          ...
          ]
    ```

Add a new view function, that returns the appropriate page

```fsharp
let view (model: Model) (dispatch: Msg -> unit) =
    match model.CurrentPage with
    | TodoList -> todoView model dispatch
    | NotFound -> Bulma.box "Page not found"
    
```

!!! info "Adding UI elements to every page of the website"
    In this recipe, we moved all the page content to the `todoView`, but you don't have to. You can add UI you want to display on every page of the application to the `view` function.

## 7. Adding the React router to the view

Add the `React.Router` element as the outermost element of the view. Dispatch the PageChanged event on `onUrlChanged`

=== "Code"
    ```fsharp
    let view (model: Model) (dispatch: Msg -> unit) =
        React.router [
            router.onUrlChanged (parseUrl >> PageChanged >> dispatch)
            router.children [
                match model.CurrentPage with
                ...
            ]
        ]
    ```
=== "Diff"
    ```.diff
      let view (model: Model) (dispatch: Msg -> unit) =
    +     React.router [
    +         router.onUrlChanged (parseUrl >> PageChanged >> dispatch)
              router.children [
                  match model.CurrentPage with
                  ...
              ]
          ]
    ```

## 9.  Try it out 

The routing should work now. Try navigating to [localhost:8080](http://localhost:8080/); you should see a page with "Page not Found". If you go to [localhost:8080/#/todo](http://localhost:8080/#/todo), you should see the todo app.

!!! info "# sign"
    You might be surprised to see the hash sign as part of the url. It enables React to react to url changes without a full page refresh.
    There are ways to omit this, but getting this to work properly is outside of the scope of this recipe.

## 10. Adding more pages

Now that you have set up the routing, adding more pages is simple: add a new case to the `Page` type; add a route for this page in the `ParseUrl` function; add a function that takes a model and dispatcher to generate your new page, and add a new case to the pattern match inside the view to display the new case.

