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

#### 2. JavaScript Interop

Insert the following lines after the *open* statements. This function will be used for JavaScript interop. [The Emit attribute](https://fable.io/docs/communicate/js-from-fable.html#Emit-when-F-is-not-enough) means that the calls to this function will be replaced by inlined JavaScript code.

```fsharp
[<Emit("new Uint8Array($0)")>]
let createByteArray arrBuff : byte[] = jsNative
```

#### 3. File Event Handler

Then, add the following. The `reader.onload` block will be executed once we select and confirm a file to be uploaded. Read [the FileReader docs](https://developer.mozilla.org/en-US/docs/Web/API/FileReader) to find out more.

```fsharp
let handleFileEvent onLoad (fileEvent:Browser.Types.Event) =
    let files:Browser.Types.FileList = !!fileEvent.target?files
    if files.length > 0 then
        let reader = Browser.Dom.FileReader.Create()
        reader.onload <- (fun _ -> createByteArray reader.result |> onLoad)
        reader.readAsArrayBuffer(files.[0])
```



#### 4. Create the UI Element

> This step varies depending on whether you're using the standard or the minimal template. Apply only the instructions under the appropriate heading.

---

##### I'm using the standard template

Insert the following block of code at the end of `FileUpload.fs`. This function will create a UI element to be used to upload files. [Click here](https://bulma.io/documentation/form/file/) to find out more about Bulma's file input component.

```fsharp
open Fulma

let createFileUpload onLoad =
    File.file [] [
        File.label [] [
            File.input [
                Props [ OnChange (handleFileEvent onLoad) ]
            ]
            File.cta [] [ str "Select File" ]
        ]
    ]
```

---

##### I'm using the minimal template

Insert the following block of code at the end of `FileUpload.fs`. This function will create a UI element to be used to upload files.

```fsharp
let createFileUpload onLoad =
    input [
        Type "file"
        OnChange (handleFileEvent onLoad)
    ]
```

---

#### 5. Use the UI Element

Having followed all these steps, you can now use the `createFileUpload` function in `Index.fs` to create the UI element for uploading files. One thing to note is that `HandleFile` is a case of the discriminated union type `Msg` that's in `Index.fs`. You can use this message case to [send the file from the client to the server](http://localhost:8000/recipes/client-server/messaging-post/).

```fsharp
FileUpload.createFileUpload (HandleFile >> dispatch)
```

