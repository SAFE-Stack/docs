#How do I debug a SAFE app?

The way in which you debug a SAFE app varies between the minimum and standard templates and also between IDEs.

---

## **I'm using the minimal template with Visual Studio**

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

Navigate to the **Client** project directory and run 

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

## **I'm using the standard template with Visual Studio**

If you are using the standard template, the process is mostly the same. The only difference is that instead of running
```powershell
npm install
npm run start
```
you just run
```powershell
yarn install
yarn run start
```

## **I'm using VS Code**

VS Code allows you to take advantage of [full stack debugging](../../../feature-debugging), so you can seamlessly step through both Client and Server code in a single session.