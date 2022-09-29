# How do I debug a SAFE app?

## I'm using Visual Studio
In order to debug Server code from Visual Studio, we need set the correct URLs in the project's debug properties.

### Debugging the Server

#### 1. Configure launch settings
You can do this through the Server project's **Properties/Debug** editor or by editing the `launchSettings.json` file which is in the **properties** folder.

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
Although we write our client-side code using F#, it is being converted into Javascript at runtime by Fable and executed in the browser.
However, we can still debug it via the magic of source mapping. If you are using Visual Studio, you cannot directly connect to the browser debugger. You can, however, debug your client F# code using the browser's development tools.

#### 1. Set breakpoints in Client code
The exact instructions will depend on your browser, but essentially it simply involves:

* Opening the Developer tools panel (usually by hitting F12).
* Finding the F# file you want to add breakpoints to in the source of the website (look inside the webpack folder).
* Add breakpoints to it in your browser inspector.

## I'm using VS Code
VS Code allows "full stack" debugging i.e. both the client and server. Prerequisites that you should install:

#### 0. Install Prerequisites

* **Install** either [Google Chrome](https://www.google.com/chrome/) or [Microsoft Edge](https://www.microsoft.com/en-us/edge): Enables client-side debugging.
* **Configure your browser** with the following extensions:
    * [Redux Dev Tools](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en): Provides improved debugging support in Chrome with Elmish and access to Redux debugging.
    * [React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi/related): Provides access to React debugging in Chrome.
* **Configure VS Code** with the following extensions:
    * [Ionide](https://marketplace.visualstudio.com/items?itemName=Ionide.Ionide-fsharp): Provides F# support to Code.
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp): Provides .NET Core debugging support.
    * [Debugger for Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome): Provides integrated client-side debugging in Code.
    * [Debugger for Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge): Provides integrated client-side debugging in Code.

#### 1. Create a launch.json file
Open the Command Palette using `Ctrl+Shift+P` and run `Debug: Open launch.json`. This will ask you to choose a platform; select `.NET Core`.

This will create a `launch.json` file in the root of your solution and also open it in the editor.

#### 2. Add a Configuration
Click the "Add Configuration..." button and choose **Launch .NET Core Console Application**.
The only change required is to point it at the Server application, by replacing the `program` line with this:

```json
"program": "${workspaceFolder}/src/Server/bin/Debug/net5.0/Server.dll",
```

#### 3. Configure a build task
* From the Command Palette, choose `Configure Task`.
* Select `Create tasks.json file from template`. This will show you a list of pre-configured templates.
* Select `.NET Core`.
* Update the build directory using `"options": {"cwd": "src/Server"},` as shown below:

```json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "command": "dotnet",
            "type": "shell",
            "options": {"cwd": "src/Server"}, 
            "args": [
                "build",
                "debug-pt3.sln",
                // Ask dotnet build to generate full paths for file names.
                "/property:GenerateFullPaths=true",
                // Do not generate summary otherwise it leads to duplicate errors in Problems panel
                "/consoleloggerparameters:NoSummary"
            ],
            "group": "build",
            "presentation": {
                "reveal": "silent"
            },
            "problemMatcher": "$msCompile"
        }
    ]
}
```


#### 4. Debug the Server
Either hit F5 or open the Debugging pane and press the Play button to build and launch the Server with the debugger attached.
Observe that the Debug Console panel will show output from the server. The server is now running and you can set breakpoints and view the callstack etc.

#### 5. Debug the Client
* Start the Client using `dotnet fable watch src/Client --run webpack-dev-server`.
* Open the Command Palette and run `Debug: Open Link`.
* When prompted for a url, type `http://localhost:8080/`. This will launch a browser which is pointed at the URL and connect the debugger to it.
* You can now set breakpoints in the generated `.fs.js` files within VS Code.
* Select the appropriate Debug Console you wish to view.

> If you find that your breakpoints aren't being hit, try stopping the Client, disconnecting the debugger and re-launching them both.

> To find out more about the VS Code debugger, see [here](https://code.visualstudio.com/docs/editor/debugging).