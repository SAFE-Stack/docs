# Serve a file from the back-end

In SAFE apps, you can send a file from the server to the client as well as you can send any other type of data. However, there are a few details that make this case unique that varies on whether you use the standard or the minimal template.



## I am using the minimal template

#### 1. Add the route

To begin, find the `Route` module in *Shared.fs* and create the following route inside it.

```fsharp
let file = "api/file"
```

#### 2. HTTP Handler

Find the `webApp` in *Server.fs*. Inside its `router` expression, add the following `get` expression.

```fsharp
open FSharp.Control.Tasks.V2

let webApp =
    router {
        //...other handlers
        get Route.file (fun next ctx ->
            task {
                let byteArray = System.IO.File.ReadAllBytes("~/files/file.xlsx")
                ctx.SetContentType "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
                ctx.SetHttpHeader "Content-Disposition" "attachment;"
                return! ctx.WriteBytesAsync (byteArray)
            })
    }
```

What we're doing here is to read a file from the local drive, but where the file is retrieved from is irrelevant. Then, using `ctx`, which is of type `HttpContext`, we let the browser know about the type of data this handler is returning. The last line (again, using `ctx`) writes a byte array to the body of the HTTP response as well as handling some details that goes alongside this process.

#### 3. The download function

Although not perfect, the best solution for handling the file download is creating an invisible download link, clicking it, and then removing it completely. The following block of code is all we need for this. Add it to the *Index.fs* file, somewhere above the `view` function.

```fsharp
open Fable.Core.JsInterop
open Shared

let downloadFile () =
    let anchor = Browser.Dom.document.createElement "a"
    anchor?style <- "display: none"
    anchor?href <- Route.file
    anchor?download <- "MyFile.xlsx"
    anchor.click()
    anchor.remove()
```

> You could also pass in the name of the file or the route to be hit as a parameter.

Now, you can call the `downloadFile` function to initiate the file download.



## I am using the standard template

#### 1. Define the route

Since the standard template uses Fable.Remoting, we need to edit our API definition first. Find your API **type** definition in `Shared.fs`. It's usually the last block of code. The one you see here is named IFileAPI, but the one you see in `Shared.fs` will be named differently. Edit this definition to have the `download` member you see below.

```fsharp
open System // We need this for the `Byte` type

type IFileAPI =
	{ //...other routes 
	  download : unit -> Async<Byte[]> }
```

#### 2. Add the route

Open the *Server.fs* file and find the API that implements the definition we've just edited. It should now have an error since we're not matching the definition at the moment. Add the following route to it

```fsharp
download = fun () -> async {
    let byteArray = System.IO.File.ReadAllBytes("~/files/file.xlsx")
    return byteArray
}
```

> Observe that this matches the route we've just defined inside the API definition, in that it takes in a unit and returns a byte array.

#### 3. The download function

Paste the following code into `Index.fs`, somewhere above the `view` function.

```fsharp
open Browser.Dom
open Fable.Core
open Fable.Core.JsInterop

[<Emit("new Blob([$0.buffer], { 'type': $1 })")>]
let createBlobFromBytes (bytes: byte[]) (contentType: string) : Browser.Types.Blob = jsNative

[<Emit("window.URL.createObjectURL($0)")>]
let createObjectUrl (blob: Browser.Types.Blob) : string = jsNative

let downloadFile () =
    async {
        let! fileContents = fileApi.download ()
        let contentType = "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
        let blob = createBlobFromBytes fileContents contentType
        let dataUrl = createObjectUrl blob
        let anchor = document.createElement "a"
        anchor?style <- "display: none"
        anchor?href <- dataUrl
        anchor?download <- "MyFile.xlsx"
        anchor.click()
        anchor.remove()
    }
```

If you looked at the minimal template's download function, you will realise that this snippet is quite larger. The main difference is that in this example we are using a couple of JavaScript interop functions to create a *blob* from the *byte array* that we receive from the server and to generate a URL to download it.

#### 4. Using the download funciton

Since the `downloadFile` function is asynchronous, we can't just call it anywhere in our view. The way we're going to deal with this is to create a `Msg` case and handle it in our `update` funciton.

##### a. Add a couple of new cases to the Msg type

```fsharp
type Msg =
    //...other cases
    | DownloadFile
    | FileDownloaded
```

##### b. Handle these cases in the update function

```fsharp
let update (msg: Msg) (model: Model): Model * Cmd<Msg> =
		match msg with
    //...other cases
    | DownloadFile -> model, Cmd.OfAsync.perform downloadFile () FileDownloaded
    | FileDownloaded -> model, Cmd.none // You can do something else here
```

##### c. Dispatch this *message* using a UI element

```fsharp
Html.button [
    prop.OnClick (fun _ -> dispatch DownloadFile)
    prop.text "Click to download" 
]
```

Having added this last snippet of code into the `view` function, you will be able to download the file by clicking the button that will now be displayed in your UI.



