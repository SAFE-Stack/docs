# How do I debug a SAFE app?

## I'm using Visual Studio
In order to debug Server code from Visual Studio, we need set the correct URLs in the project's debug properties.

### Debugging the Server

#### 1. Configure launch settings
You can do this through the Server project's **Properties/Debug** editor or by editing the `launchSettings.json` file in `src/Server/Properties`

After selecting the debug profile that you wish to edit (**IIS Express** or **Server**), you will need to set the **App URL** field to `http://localhost:5000` and **Launch browser** field to `http://localhost:8080`. The process is [very similar](https://docs.microsoft.com/en-us/visualstudio/mac/launch-settings?view=vsmac-2019#configure-the-start-url) for VS Mac.

Once this is done, you can expect your `launchSettings.json` file to look something like this:
```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 44330
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "launchUrl": "http://localhost:8080/",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "Server": {
      "commandName": "Project",
      "launchBrowser": true,
      "launchUrl": "http://localhost:8080",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:5000"
    }
  }
}
```

#### 2. Start the Client
Since you will be running the server directly through Visual Studio, you cannot use a FAKE script to start the application, so launch the client directly using e.g. `npm run start`.

#### 3. Debug the Server
Set the server as your Startup project, either using the drop-down menu at the top of the IDE or by right clicking on the project itself and selecting **Set as Startup Project**. Select the profile that you set up earlier and wish to launch from the drop-down at the top of the IDE. Either press the Play button at the top of the IDE or hit F5 on your keyboard to start the Server debugging and launch a browser pointing at the website.

### Debugging the Client
Although we write our client-side code using F#, it is being converted into JavaScript at runtime by Fable and executed in the browser.
However, we can still debug it via the magic of source mapping. If you are using Visual Studio, you cannot directly connect to the browser debugger. You can, however, debug your client F# code using the browser's development tools.

#### 1. Set breakpoints in Client code
The exact instructions will depend on your browser, but essentially it simply involves:

* Opening the Developer tools panel (usually by hitting F12).
* Finding the F# file you want to add breakpoints to in the source of the website.
* Add breakpoints to it in your browser inspector.

## I'm using VS Code
VS Code allows "full stack" debugging i.e. both the client and server. Prerequisites that you should install:

#### 0. Install Prerequisites

* **Install** either [Google Chrome](https://www.google.com/chrome/) or [Microsoft Edge](https://www.microsoft.com/en-us/edge): Enables client-side debugging.
* **Configure your browser** with the following extensions:
    * [Redux Dev Tools](https://chromewebstore.google.com/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en): Provides improved debugging support in Chrome with Elmish and access to Redux debugging.
    * [React Developer Tools](https://chromewebstore.google.com/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi): Provides access to React debugging in Chrome.
* **Configure VS Code** with the following extensions:
    * [Ionide](https://marketplace.visualstudio.com/items?itemName=Ionide.Ionide-fsharp): Provides F# support to Code.
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp): Provides .NET Core debugging support.

#### Debug the Server

1. Click the debug icon on the left hand side, or hit `ctrl+shift+d` to open the debug pane.

2. In the bar with the play error, where it says "No Configurations", use the dropdown to select ".NET 5 and .NET Core". In the dialog that pops up, select  "Server.Fsproj: Server"

3. Hit F5

The server is now running. You can use the bar at the top of your screen to pause, restart or kill the debugger

#### 5. Debug the Client

1. Start the Client by running `dotnet fable watch -o output -s --run npx vite` from `<repo root>/src/Client/`. 
2. Open the Command Palettek using `ctrl+shift+p` and run `Debug: Open Link`. 
3. When prompted for a url, type `http://localhost:8080/`. This will launch a browser which is pointed at the URL and connect the debugger to it. 
4. You can now set breakpoints in by opening files via the "Loaded Scrips" tab in the debugger; setting breakpoints in files opened from disk does NOT work.

> If you find that your breakpoints aren't being hit, try stopping the Client, disconnecting the debugger and re-launching them both.

> To find out more about the VS Code debugger, see [here](https://code.visualstudio.com/docs/editor/debugging).