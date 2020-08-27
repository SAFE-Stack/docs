# How do I upgrade from SAFE v1 to v2?

There have been a number of changes between the first and second major versions of the SAFE template.

However, it isn't to difficult to update an existing v1 style project to the v2 structure if you wish to do so.

> If you haven't done so already then you will need to install the prequisites listed in the [Quick Start](http://localhost:8000/quickstart/) guide.

#### 1. Install the v2 template

Download and install the Safe V2 template by running the following command:

```powershell
dotnet new -i SAFE.Template
```

#### 2. Create a v2 project

Create a new project from the v2 template. We will use this as a basis for our conversion.

Run the command
```fsharp
dotnet new SAFE
```
to create a new project.

#### 3. Update dotnet tools

Open the v1 project's root directory. You will find a folder called `.config` which contains a file named `dotnet-tools.json`. This will list any dotnet tooling required to run the project.

Copy the contents of the equivalent file in the v2 template and overwrite the v1 file contents with them.

> If you have installed dotnet tools in addition to those that come with the v1 template, you will need to make sure to continue to include them when you do this replacement.

#### 4. Replace Yarn with NPM


