#How do I debug a SAFE app?

## Debugging the Server

### I'm using Visual Studio

In order to debug Server code from Visual Studio, we need set the correct URLs in the project's debug properties.

#### 1. Configure launch settings

You can do this through the Server project's **Properties/Debug** editor or by editing the `launchSettings.json` file which is in the **properties** folder.

After selecting the debug profile that you wish to edit (**IIS Express** or **Server**), you will need to set the **App URL** field to `http://localhost:8080` and **Launch browser** field to `http://localhost:8085`.

The process is [very similar](https://docs.microsoft.com/en-us/visualstudio/mac/launch-settings?view=vsmac-2019#configure-the-start-url) for VS Mac.

Once this is done, you can expect your `launchSettings.json` file to look something like this:
```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:8085/",
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
      "applicationUrl": "http://localhost:8085"
    }
  }
}
```

#### 2. Start the Client

Navigate to the root of the solution and run 

```powershell
npm install
```
followed by

```powershell
npm run start
```

#### 3. Start the Server

Set the server as your Startup project, either using the drop-down menu at the top of the IDE or by right clicking on the project itself and selecting **Set as Startup Project**. 

Select the profile that you set up earlier and wish to launch from the drop-down at the top of the IDE.

Either press the Play button at the top of the IDE or hit F5 on your keyboard to start the Server debugging and launch a browser pointing at the website.


### I'm using VS Code

In order to debug code from VS Code, we first need set up a .NET Core debug configuration.


#### 1. Create a launch.json file

VS Code provides prebuilt debug configurations for most types of project, so you can just fill in the details which are specific to your application.

Open the Command Palette using `Ctrl+Shift+P` and run `Debug: Open launch.json`. 

This will ask you to choose a platform; select `.NET Core`, which will create a `launch.json` file in the root of your solution and also open it in the editor.


#### 2. Add a Configuration

When viewing the launch.json file you just created, click the button at the bottom of the page labelled `Add Configuration`.

This will offer you a choice of pre-built configurations. Select `.NET: Launch a .NET Core Console App`.

The only change required is to point it at the Server application.

Replace the `program` line with this:
```json
"program": "${workspaceFolder}/src/Server/bin/Debug/netcoreapp3.1/Server.dll",
```

#### 3. Configure a build task

If you press F5, VS Code will try to launch the debugger, but it will pop up an error `Could not find the task 'build.`.

Click the `Configure Task` button in this error dialog.

This will show you a list of pre-built build tasks. Select `Create tasks.json file from template`.

This will show you a list of pre-configured templates. Select `.NET Core`.


#### 4. Start the Client

Navigate to the root of the solution and run 

```powershell
npm install
```
followed by

```powershell
npm run start
```

#### 5. Start the Server

Either hit F5 or open the Debugging pane and press the Play button to launch the Server with the debugger attached.

Navigate to `http://localhost:8080/` in your browser as usual to view the running application.


## Debugging the Client

Although we write our client-side code using F#, it is being converted into Javascript at runtime by Fable.

However, we can still debug it, via the magic of source mapping.

### I'm using Visual Studio

If you are using Visual Studio, you cannot directly connect to the browser debugger. However, you can still inspect and debug the F# using the browser debugging tools.

#### 1. Start the Client

Navigate to the root of the solution and run 

```powershell
npm install
```
followed by

```powershell
npm run start
```

#### 2. Start the Server

Navigate to the Server project directory and launch it using
```powershell
dotnet run
```

#### 3. Set breakpoints in Client code

The exact instructions will depend on your browser, but essentially it simply involves 

- Opening the Developer tools panel (usually by hitting F12).

- Finding the F# file you want to add breakpoints to in the source of the website (look inside the webpack folder).

- Add breakpoints to it in your browser inspector.

### I'm using VS Code

VS Code integrates with the Chrome debugger, which means you can debug Client code directly within in the IDE.

- Start the Client and Server in the same way as described above for Visual Studio.

- Open the Command Palette using `Ctrl+Shift_P` and run `Debug: Open Link`. When prompted for a url, type `http://localhost:8080/`. This will launch a browser which is pointed at the URL connect the debugger to it.

> This command is only available in the July 2020 release of VS Code or later.

- You can now set breakpoints in your Client within VS Code.

> If you find that your breakpoints aren't being hit, try stopping the Client, disconnecting the debugger and re-launching them both.



### Full Stack Debugging

If you are using VS Code, you can combine the Server and Client instructions shown above, allowing you to debug both the Client and Server at the same time.

Just follow the Client debugging instructions, but instead of using `dotnet run` to launch the server, press F5 or use the Debugging pane to launch it using the `launch.json` / `tasks.json` profiles you set up earlier.