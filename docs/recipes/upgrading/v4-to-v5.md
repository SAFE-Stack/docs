# How do I upgrade from SAFE v4 to v5?

### F# tools and dependencies

1. **Get the latest dotnet tools such as Fable and Fantomas into your repository**.
	1. Overwrite your `dotnet-tools.json` file [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/.config/dotnet-tools.json).
	1. Ensure tools have been downloaded to your machine with `dotnet tool restore`.

1. **Use our preferred F# formatting style**.
	1. Overwrite your `.editorconfig` file [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/.editorconfig).

1. **Migrate all dependencies to .NET 8**.
	1. Overwrite your `global.json` file [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/global.json).

	1. Update each of your project files to target .NET 8.

	    ```xml
	    <PropertyGroup>
	        <TargetFramework>net8.0</TargetFramework>
	    </PropertyGroup>
	    ```

	1. Upgrade all .NET dependencies to the latest versions for SAFE v5:

		1. Run `dotnet paket remove Fable.React -p Client`.
		1. Run `dotnet paket remove Feliz.Bulma -p Client`.
		1. Overwrite your `paket.dependencies` file [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/paket.dependencies).
		1. Overwrite your `paket.lock` file [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/paket.lock).
		1. Overwrite your Shared project's `paket.references` file [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/src/Shared/paket.references).
		1. Run `dotnet paket install` to update the Shared project.
		1. Manually re-add any custom dependencies that you previously had in any projects (Client, Server or Shared etc.):
			1. `cd` into the required project.
			1. `dotnet paket add <package> --keep-minor`. This will download the latest version of the package you required *but will not update any associated dependencies outside of their existing major version*.

### Javascript tools and dependencies
1. **Update all dependencies.**
	1. Replace `package.json` with [this file](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/package.json).
	1. Replace `package-lock.json` with [this file](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/package-lock.json).
	1. Install Node v18 or v20 and NPM v9 or v10.
	1. Re-add any NPM packages that you previously had.
1. **Migrate from webpack to vite**.
	1. Delete `webpack.config.js`
	1. Add the `src/Client/vite.config.mts` file [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/src/Client/vite.config.mts).

### Styling configuration
1. **Install Tailwind**.
	1. Run `npx tailwindcss init -p` in `src/Client`
	1. Add the `src/Client/tailwind.config.js` file [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/src/Client/tailwind.config.js).
	1. Add the `src/Client/index.css` file [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/src/Client/index.css).

1. **Update HTML and F# code**.
	1. Overwrite `src/Client/index.html` with [this file](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/src/Client/index.html).
	1. Add the following lines at the top of `src/Client/App.fs`, after the existing open declarations
		```fsharp
		open Fable.Core.JsInterop

		importSideEffects "./index.css"
		```

### Automated tests
1. Add the file `tests/Client/vite.config.mts` [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/tests/Client/vite.config.mts).
1. Overwrite the `tests/Client/index.html` file [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/tests/Client/index.html).
1. Add the file `.fantomasignore` [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/.fantomasignore).

### Automated build
1. In the `Build.fs` file replace the following lines:

	Line 27:

	```diff
	- "client", dotnet [ "fable"; "-o"; "output"; "-s"; "--run"; "npm"; "run"; "build" ] clientPath ]
	+ "client", dotnet [ "fable"; "-o"; "output"; "-s"; "--run"; "npx"; "vite"; "build" ] clientPath ]
	```

	Line 35:

	```diff
	- operating_system OS.Windows
	- runtime_stack Runtime.DotNet60
	+ operating_system OS.Linux
	+ runtime_stack (DotNet "8.0")
	```

	Line 51:

	```diff
	- "client", dotnet [ "fable"; "watch"; "-o"; "output"; "-s"; "--run"; "npm"; "run"; "start" ] clientPath ]
	+ "client", dotnet [ "fable"; "watch"; "-o"; "output"; "-s"; "--run"; "npx"; "vite" ] clientPath ]
	```

	Line 58:

	```diff
	- "client", dotnet [ "fable"; "watch"; "-o"; "output"; "-s"; "--run"; "npm"; "run"; "test:live" ] clientTestsPath ]
	+ "client", dotnet [ "fable"; "watch"; "-o"; "output"; "-s"; "--run"; "npx"; "vite" ] clientTestsPath ]
	```

	**Note**: If you are using a template created prior to version v4.3, you may have the following string syntax for the `dotnet` commands and therefore the change you need to make will be slightly different.

	```diff
	- "client", dotnet "fable -o output -s --run npm run build" clientPath
	+ "client", dotnet "fable -o output -s --run npx vite build" clientPath
	```

### Additional resources
1. **VSCode Tailwind intellisense**.
	1. Install the `Tailwind CSS Intellisense` extension.
	1. Create the `.vscode/settings.json` file [from here](https://raw.githubusercontent.com/SAFE-Stack/SAFE-template/master/Content/default/.vscode/settings.json). *The regexes in this file are for Feliz style DSL, if you want to support Fable.React DSL you will need to adapt the regexes.*