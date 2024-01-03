## Install dependencies

First off, you need to create a SAFE app, [install the relevant dependencies](https://mangelmaxime.github.io/Fable.Form/Fable.Form.Simple.Bulma/installation.html), and wire them up to be available for use in your F# Fable code.

1. Create a new SAFE app and restore local tools:
```sh
dotnet new SAFE
dotnet tool restore
```
1. Add bulma to your project:
follow [this recipe](../ui/add-bulma.md)

1. Install Fable.Form.Simple.Bulma using Paket:
```sh
dotnet paket add Fable.Form.Simple.Bulma -p Client
```

1. Install bulma and fable-form-simple-bulma npm packages:
```sh
npm add fable-form-simple-bulma
npm add bulma
```

## Register styles

1. Rename `src/Client/Index.css` to `Index.scss`

2. Update the import in `App.fs`

    === "Code"    
        ```.fs title="App.fs"
        ...
        importSideEffects "./index.scss"
        ...
        ```
    === "Diff"    
        ```.diff title="App.fs" 
        ...
        - importSideEffects "./index.css"
        + importSideEffects "./index.scss"
        ...
        ```

3. Import bulma and fable-form-simple in `Index.scss`

    ``` .scss title="Index.scss"
    @import "~bulma";
    @import "~fable-form-simple-bulma";
    ...
    ```

2. Remove the Bulma stylesheet link from `./src/Client/index.html`, as it is no longer needed:

    ``` { .diff title="index.html" }
        <link rel="icon" type="image/png" href="/favicon.png"/>
    -   <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.9.0/css/bulma.min.css">
        <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.14.0/css/all.min.css">
    ```

## Replace the existing form with a Fable.Form

With the above preparation done, you can use Fable.Form.Simple.Bulma in your `./src/Client/Index.fs` file.

1. Open the newly added namespaces:

    ``` { .fsharp title="Index.fs" }
    open Fable.Form.Simple
    open Fable.Form.Simple.Bulma
    ```


1. Create type `Values` to represent each input field on the form (a single textbox), and create a type `Form` which is an alias for `Form.View.Model<Values>`:


    ``` { .fsharp title="Index.fs" }
    type Values = { Todo: string }
    type Form = Form.View.Model<Values>
    ```

1. In the `Model` type definition, replace `Input: string` with `Form: Form`  

    === "Code"
        ```  { .fsharp title="Index.fs" }
        type Model = { Todos: Todo list; Form: Form }
        ```

    === "Diff"
        ```  { .diff title="Index.fs" }
        -type Model = { Todos: Todo list; Input: string }
        +type Model = { Todos: Todo list; Form: Form }
        ```

1. Update the `init` function to reflect the change in `Model`:

    === "Code"
        ```  { .fsharp title="Index.fs" }
        let model = { Todos = []; Form = Form.View.idle { Todo = "" } }
        ```

    === "Diff"
        ```  { .diff title="Index.fs" }
        -let model = { Todos = []; Input = "" }
        +let model = { Todos = []; Form = Form.View.idle { Todo = "" } }
        ```

1. Change `Msg` discriminated union - replace the `SetInput` case with `FormChanged of Form`, and add string data to the `AddTodo` case:

    === "Code"
        ``` { .fsharp title="Index.fs" }
        type Msg =
            | GotTodos of Todo list
            | FormChanged of Form
            | AddTodo of string
            | AddedTodo of Todo
        ```

    === "Diff"
        ``` { .diff title="Index.fs" }
        type Msg =
            | GotTodos of Todo list
        -   | SetInput of string
        -   | AddTodo
        +   | FormChanged of Form
        +   | AddTodo of string
            | AddedTodo of Todo
        ```

1. Modify the `update` function to handle the changed `Msg` cases:

    === "Code"
        ``` { .fsharp title="Index.fs" }
        let update (msg: Msg) (model: Model) : Model * Cmd<Msg> =
            match msg with
            | GotTodos todos -> { model with Todos = todos }, Cmd.none
            | FormChanged form -> { model with Form = form }, Cmd.none
            | AddTodo todo ->
                let todo = Todo.create todo
                let cmd = Cmd.OfAsync.perform todosApi.addTodo todo AddedTodo
                model, cmd
            | AddedTodo todo ->
                let newModel =
                    { model with
                        Todos = model.Todos @ [ todo ]
                        Form =
                            { model.Form with
                                State = Form.View.Success "Todo added"
                                Values = { model.Form.Values with Todo = "" } } }
                newModel, Cmd.none
        ```

    === "Diff"
        ``` { .diff title="Index.fs" }
        let update (msg: Msg) (model: Model) : Model * Cmd<Msg> =
            match msg with
            | GotTodos todos -> { model with Todos = todos }, Cmd.none
        -   | SetInput value -> { model with Input = value }, Cmd.none
        +   | FormChanged form -> { model with Form = form }, Cmd.none
        -   | AddTodo ->
        -       let todo = Todo.create model.Input
        -       let cmd = Cmd.OfAsync.perform todosApi.addTodo todo AddedTodo
        -       { model with Input = "" }, cmd
        +   | AddTodo todo ->
        +       let todo = Todo.create todo
        +       let cmd = Cmd.OfAsync.perform todosApi.addTodo todo AddedTodo
        +       model, cmd
        -   | AddedTodo todo -> { model with Todos = model.Todos @ [ todo ] }, Cmd.none
        +   | AddedTodo todo ->
        +       let newModel =
        +           { model with
        +               Todos = model.Todos @ [ todo ]
        +               Form =
        +                   { model.Form with
        +                       State = Form.View.Success "Todo added"
        +                       Values = { model.Form.Values with Todo = "" } } }
        +       newModel, Cmd.none
        ```


1. Create `form`. This defines the logic of the form, and how it responds to interaction:

    ``` { .fsharp title="Index.fs" }
    let form : Form.Form<Values, Msg, _> =
        let todoField =
            Form.textField
                {
                    Parser = Ok
                    Value = fun values -> values.Todo
                    Update = fun newValue values -> { values with Todo = newValue }
                    Error = fun _ -> None
                    Attributes =
                        {
                            Label = "New todo"
                            Placeholder = "What needs to be done?"
                            HtmlAttributes = []
                        }
                }

        Form.succeed AddTodo
        |> Form.append todoField
    ```

1. In the function `todoAction`, remove the existing form view. Then replace it using `Form.View.asHtml` to render the view:

    === "Code"
         ```  { .fsharp title="Index.fs" }  
         let private todoAction model dispatch =
             Form.View.asHtml
                 {
                     Dispatch = dispatch
                     OnChange = FormChanged
                     Action = Action.SubmitOnly "Add"
                     Validation = Validation.ValidateOnBlur
                 }
                 form
                 model.Form
         ```
    === "Diff"
           ```  { .diff title="Index.fs" }
             let private todoAction model dispatch =
           -      Html.div [
           -      ...
           - ]
           +    Form.View.asHtml
           +       {
           +           Dispatch = dispatch
           +           OnChange = FormChanged
           +           Action = Action.SubmitOnly "Add"
           +           Validation = Validation.ValidateOnBlur
           +       }
           +       form
           +       model.Form
           ```


## Adding new functionality

With the basic structure in place, it's easy to add functionality to the form. For example, the [changes](https://github.com/CompositionalIT/safe-fable-form/commit/6342ee8f4abcfeed6dd5066718e6845e6e2174d0) necessary to add a high priority checkbox are pretty small.