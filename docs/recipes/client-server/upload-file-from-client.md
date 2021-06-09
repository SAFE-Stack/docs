# How do I upload a file from the client?
[Fable](https://fable.io/) makes it quick and easy to upload files from the client. Both the standard and the minimal template comes with Fable support by default.

---

#### 1. Create a File

Create a file in the client project named `FileUpload.fs` somewhere before the `Index.fs` file and insert the following:

```fsharp
module FileUpload

open Fable.React
open Fable.React.Props
open Fable.FontAwesome
open Fable.Core
open Fable.Core.JsInterop
```



#### 2. File Event Handler

Then, add the following. The `reader.onload` block will be executed once we select and confirm a file to be uploaded. Read [the FileReader docs](https://developer.mozilla.org/en-US/docs/Web/API/FileReader) to find out more.

```fsharp
let handleFileEvent onLoad (fileEvent:Browser.Types.Event) =
    let files:Browser.Types.FileList = !!fileEvent.target?files
    if files.length > 0 then
        let reader = Browser.Dom.FileReader.Create()
        reader.onload <- (fun _ -> reader.result |> unbox |> onLoad)
        reader.readAsArrayBuffer(files.[0])
```



#### 3. Create the UI Element

> This step varies depending on whether you're using the standard or the minimal template. Apply only the instructions under the appropriate heading.

---

##### I'm using the standard template

Insert the following block of code at the end of `FileUpload.fs`. This function will create a UI element to be used to upload files. [Click here](https://bulma.io/documentation/form/file/) to find out more about Bulma's file input component.

```fsharp
open Feliz.Bulma

let createFileUpload onLoad =
    Bulma.file [
        Bulma.fileLabel.label [
            Bulma.fileInput [
                prop.onChange (handleFileEvent onLoad)
            ]
            Bulma.fileCta [
                Bulma.fileLabel.label "Choose a file..."
            ]
        ]
    ]
```

---

##### I'm using the minimal template

Insert the following block of code at the end of `FileUpload.fs`. This function will create a UI element to be used to upload files.

```fsharp
let createFileUpload onLoad =
    let input = document.createElement "INPUT"
    input.onchange <- (handleFileEvent onLoad)
```

---



#### 4. Use the UI Element

Having followed all these steps, you can now use the `createFileUpload` function in `Index.fs` to create the UI element for uploading files. One thing to note is that `HandleFile` is a case of the discriminated union type `Msg` that's in `Index.fs`. You can use this message case to [send the file from the client to the server](./messaging-post).

```fsharp
FileUpload.createFileUpload (HandleFile >> dispatch)
```

