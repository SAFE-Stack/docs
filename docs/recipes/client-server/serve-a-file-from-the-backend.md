# Serve a file from the back-end

In SAFE apps, you can send a file from the server to the client as well as you can send any other type of data.

#### 1. Define the route

Since the standard template uses Fable.Remoting, we need to edit our API definition first. Find your API **type** definition in `Shared.fs`. It's usually the last block of code. The one you see here is named ITodoAPI. Edit this definition to have the `download` member you see below.

```fsharp
type ITodoAPI =
	{ //...other routes 
	  download : unit -> Async<byte[]> }
```

#### 2. Add the route

Open the *Server.fs* file and find the API that implements the definition we've just edited. It should now have an error since we're not matching the definition at the moment. Add the following route to it

```fsharp
//...other functions in todosApi
download =
    fun () -> async {
        let byteArray = System.IO.File.ReadAllBytes("file.txt")
        return byteArray
    }
```

> Make sure to replace "file.txt" with your file that is placed in `src/Server` or relative path

#### 3. Using the download function

Since the `download` function is asynchronous, we can't just call it anywhere in our view. The way we're going to deal with this is to create a `Msg` case and handle it in our `update` funciton.

##### a. Add a couple of new cases to the Msg type

```fsharp
type Msg =
    //...other cases
    | DownloadFile
    | FileDownloaded of byte[]
```

##### b. Handle these cases in the update function

```fsharp
let update (msg: Msg) (model: Model): Model * Cmd<Msg> =
		match msg with
    //...other cases
    | DownloadFile -> model, Cmd.OfAsync.perform todosApi.download () FileDownloaded
    | FileDownloaded file ->
        file.SaveFileAs("downloaded-file.txt")
        model, Cmd.none // You can do something else here
```

> The `SaveFileAs` function detects the mime-type/content-type automatically based on the file extension of the file input

##### c. Dispatch this *message* using a UI element

```fsharp
Html.button [
    prop.onClick (fun _ -> dispatch DownloadFile)
    prop.text "Click to download" 
]
```

Having added this last snippet of code into the `view` function, you will be able to download the file by clicking the button that will now be displayed in your UI. For more information visit the [Fable.Remoting documentation](https://zaid-ajaj.github.io/Fable.Remoting/#/advanced/file-upload-and-download)