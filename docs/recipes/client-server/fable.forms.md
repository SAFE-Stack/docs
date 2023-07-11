## Install dependencies

First off, you need to create a SAFE app, [install the relevant dependencies](https://mangelmaxime.github.io/Fable.Form/Fable.Form.Simple.Bulma/installation.html), and wire them up to be available for use in your F# Fable code.

- Create a new SAFE app and restore local tools:

```sh
dotnet new SAFE
dotnet tool restore
```

- Install Fable.Form.Simple.Bulma via Paket:

```sh
dotnet paket add --project src/Client/ Fable.Form.Simple.Bulma --version 3.0.0
```

- Install bulma and fable-form-simple-bulma npm packages:

```sh
npm add fable-form-simple-bulma
npm add bulma@0.9.0
```

- Add ./src/Client/styles.scss with the following contents:

```scss
@import "~bulma";
@import "~fable-form-simple-bulma";
```

- Update ./webpack.config.js to use the new stylesheet, per the [SAFE Stack recipe](https://safe-stack.github.io/docs/recipes/ui/add-style/). Add a `cssEntry` property to `CONFIG`:

```js
cssEntry: './src/Client/style.scss',
```

- Also in ./webpack.config.js, update the entry property of the object returned from `module.exports`:

```js
entry: isProduction ? {
    app: [resolve(CONFIG.fsharpEntry), resolve(CONFIG.cssEntry)]
} : {
    app: resolve(CONFIG.fsharpEntry),
    style: resolve(CONFIG.cssEntry)
},
```

- Remove the Bulma stylesheet link from index.html, which is no longer needed:

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.9.0/css/bulma.min.css">
```

## Replace the existing form with a Fable.Form

With the above preparation done, you can use Fable.Form.Simple.Bulma in your ./src/Client/Index.fs file. For example:

- Open the namespaces:

```fsharp
open Fable.Form.Simple
open Fable.Form.Simple.Bulma
```

- Update the `Model`:

```fsharp
type Values = { Todo: string }
type Form = Form.View.Model<Values>
type Model = { Todos: Todo list; Form: Form }
```

- Update the `init` function:

```fsharp
let model = { Todos = []; Form = Form.View.idle { Todo = "" } }
```

- Replace the `SetInput` `Msg` case:

```fsharp
| FormChanged of Form
```

- Update the `AddTodo` `Msg` case to take `string` data:

```fsharp
| AddTodo of string
```

- Update the `update` function accordingly:

```fsharp
| FormChanged form -> { model with Form = form }, Cmd.none
| AddTodo todo ->
    let todo = Todo.create todo
    model, Cmd.OfAsync.perform todosApi.addTodo todo AddedTodo
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

- Bind a Fable.Form value to `form`:

```fsharp
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

- Replace the Bulma TODO field with a Fable.Form html view:

```fsharp
Form.View.asHtml
    {
        Dispatch = dispatch
        OnChange = FormChanged
        Action = Form.View.Action.SubmitOnly "Add"
        Validation = Form.View.Validation.ValidateOnSubmit
    }
    form
    model.Form
```


## Adding new functionality

With the basic structure in place, it's easy to add functionality to the form. For example, the [changes](https://github.com/CompositionalIT/safe-fable-form/commit/6342ee8f4abcfeed6dd5066718e6845e6e2174d0) necessary to add a high priority checkbox are pretty small.
