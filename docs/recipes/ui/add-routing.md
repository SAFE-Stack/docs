# How do I add routing to a SAFE app with a shared model for all pages?

When building larger apps, you probably want different pages to be accessible through different URLs. In this recipe, we show you how to add routes to different pages of an application, including adding a "page not found" page that is displayed when an unknown URL is entered.

In this recipe we use the simplest approach to storing states for multiple pages, by creating a single state for the full app. A potential benefit of this approach is that the state of a page is not lost when navigating away from it. You will see how that works at the end of the recipe.

## 1. Adding the Feliz router

Install Feliz.Router in the client project

```bash
dotnet paket add Feliz.Router -p Client
```


To include the router in the Client, open `Feliz.Router` at the top of Index.fs

```fsharp
open Feliz.Router
```

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
=== "Diff"
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
    let todoView model dispatch =
        Html.section [
        ...
        ]
    ```
=== "Diff"
    ```.diff
    - let view model dispatch =
    + let todoView model dispatch =
          Html.section [
          ...
          ]
    ```

Add a new view function, that returns the appropriate page

```fsharp
let view model dispatch =
    match model.CurrentPage with
    | TodoList -> todoView model dispatch
    | NotFound ->
        Html.div [
            prop.className "flex flex-col items-center justify-center h-full"
            prop.text "Page not found"
        ]
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

To see how the state is maintained even when navigating away from the page, type something in the text box and move away from the page by entering another path in the address bar. Then go back to the todo page. The entered text is still there.

!!! info "# sign"
    You might be surprised to see the hash sign as part of the URL. It enables React to react to URL changes without a full page refresh.
    There are ways to omit this, but getting this to work properly is outside of the scope of this recipe.

## 10. Adding more pages

Now that you have set up the routing, adding more pages is simple: add a new case to the `Page` type; add a route for this page in the `parseUrl` function; add a function that takes a model and dispatcher to generate your new page, and add a new case to the pattern match inside the `view` function to display the new case.
