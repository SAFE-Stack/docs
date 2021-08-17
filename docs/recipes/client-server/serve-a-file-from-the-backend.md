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
type IFileAPI =
	{ //...other routes 
	  download : unit -> Async<byte[]> }
```

#### 2. Add the route

Open the *Server.fs* file and find the API that implements the definition we've just edited. It should now have an error since we're not matching the definition at the moment. Add the following route to it

```fsharp
let download () = async {
    let byteArray = System.IO.File.ReadAllBytes("/fileFolder/file.xlsx")
    return byteArray
}
```

> Make sure to replace "/fileFolder/file.xlsx" with the path to your file

#### 3. The download function

Paste the following code into `Index.fs`, somewhere above the `view` function.

```fsharp

let downloadFile () =
    async {
        let! downloadedFile = todosApi.download ()
        downloadedFile.SaveFileAs("downloaded-file.xlsx")
    }
```
> The `SaveFileAs` funcion detects the mime-type/content-type automatically based on the file extension of the file input

#### 4. Using the download funciton

Since the `downloadFile` function is asynchronous, we can't just call it anywhere in our view. The way we're going to deal with this is to create a `Msg` case and handle it in our `update` funciton.

##### a. Add a couple of new cases to the Msg type

```fsharp
type Msg =
    //...other cases
    | DownloadFile
    | FileDownloaded of unit
```

##### b. Handle these cases in the update function

```fsharp
let update (msg: Msg) (model: Model): Model * Cmd<Msg> =
		match msg with
    //...other cases
    | DownloadFile -> model, Cmd.OfAsync.perform downloadFile () FileDownloaded
    | FileDownloaded () -> model, Cmd.none // You can do something else here
```

##### c. Dispatch this *message* using a UI element

```fsharp
Html.button [
    prop.onClick (fun _ -> dispatch DownloadFile)
    prop.text "Click to download" 
]
```

Having added this last snippet of code into the `view` function, you will be able to download the file by clicking the button that will now be displayed in your UI. For more information visit the [Fable.Remoting documentation](https://zaid-ajaj.github.io/Fable.Remoting/src/upload-and-download.html)


