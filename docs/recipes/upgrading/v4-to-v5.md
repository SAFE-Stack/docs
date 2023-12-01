# How do I upgrade from SAFE v4 to v5?

1. Upgrade dotnet tools to latest version:

    - `dotnet tool update fable`
    - `dotnet tool update paket`
    - `dotnet tool update fantomas`
    - `dotnet tool update femto`
	- `dotnet tool restore`

1. Remove `fantomas-tool` by running `dotnet tool uninstall fantomas-tool`
    
1. To upgrade to .NET 8 change the `global.json` to

	```json
	{
	  "sdk": {
	    "version": "8.0.0",
	    "rollForward": "latestMinor"
	  }
	}
	```

    followed by updating each of your project files to target .NET 8

    ```xml
    <PropertyGroup>
        <TargetFramework>net8.0</TargetFramework>
    </PropertyGroup>
    ```

1. Upgrade .NET dependencies to latest versions:

	Update your `paket.dependencies` with:
		
	```
	source https://api.nuget.org/v3/index.json
	framework: net8.0
	storage: none
	
	nuget Fsharp.Core ~> 8

    //Server
	nuget Fable.Remoting.Giraffe ~> 5
	nuget Saturn ~> 0
	
    //Client
	nuget Fable.Core ~> 4
	nuget Fable.Elmish ~> 4
	nuget Fable.Elmish.React ~> 4
	nuget Fable.Elmish.Debugger ~> 4
	nuget Fable.Elmish.HMR ~> 7
	nuget Fable.Remoting.Client ~> 7
	nuget Feliz ~> 2
	
    //Build
	nuget Fake.Core.Target ~> 5
	nuget Fake.IO.FileSystem ~> 5
	nuget Farmer ~> 1

    //Testing
	nuget Fable.Mocha ~> 2
	nuget Expecto ~> 9

	//Any other packages you already have here
	```

	Update your `paket.lock` with the current file [here](https://github.com/SAFE-Stack/SAFE-template/blob/master/Content/default/paket.lock)
	
	Add a `paket.references` file to `src/Shared` with the following

	```
	FSharp.Core
	```

	- Run `dotnet paket remove Fable.React -p Client`
	- Run `dotnet paket remove Feliz.Bulma -p Client`
	- Run `dotnet paket restore`

1. Remove all the webpack related dependencies:

	```bash
	npm uninstall copy-webpack-plugin \
	    css-loader \
	    file-loader \
	    html-webpack-plugin \
	    mini-css-extract-plugin \
	    sass-loader \
	    source-map-loader \
	    style-loader \
	    webpack \
	    webpack-cli \
	    webpack-dev-server
	```

1. Update the `engines` in `package.json` to the following

	```
	"engines": {
        "node": "~18 || ~20",
        "npm": "~9 || ~10"
    }
	```

	and install the relevant version of `node` and `npm`

	**Tip**: Node Version Manage or `nvm` can be used to install and manage node/npm versions

1. Remove any existing `scripts` from the `package.json` that relate to `webpack` or `webpack-dev-server`

	```diff
	- "start": "webpack-dev-server --mode development",
	- "build": "webpack --mode production",
	- "test:live": "webpack-dev-server --mode development --env test"
	```
	
1. Update the left over NPM dependencies:

	- You can run to upgrade all at once `npx npm-check-updates -u` or if you prefer to be more conservative you can use `npx npm-check-updates -i`.
	
	If you use the second option, make sure to select `react`, `react-dom`, `sass`.
	
1. Install the new NPM dependencies

	`npm install -D vite @vitejs/plugin-react tailwindcss postcss autoprefixer @types/node`
	
1. Delete `webpack.config.js`

1. Create the file `src/Client/vite.config.mts` with the following content
	
	```ts
	import { defineConfig } from "vite";
	import react from "@vitejs/plugin-react";
	
	const proxyPort = process.env.SERVER_PROXY_PORT || "5000";
	const proxyTarget = "http://localhost:" + proxyPort;
	
	// https://vitejs.dev/config/
	export default defineConfig({
	    plugins: [react()],
	    build: {
	        outDir: "../../deploy/public",
	    },
	    server: {
	        port: 8080,
	        proxy: {
	            // redirect requests that start with /api/ to the server on port 5000
	            "/api/": {
	                target: proxyTarget,
	                changeOrigin: true,
	            }
	        }
	    }
	});
	```

1. Overwrite `src/Client/index.html` with the following content
	
	```html
	<!doctype html>
	<html>
	<head>
	    <title>SAFE Template</title>
	    <meta charset="utf-8">
	    <meta name="viewport" content="width=device-width, initial-scale=1.0">
	    <link rel="icon" type="image/png" href="/favicon.png"/>
	    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.14.0/css/all.min.css">
	</head>
	<body>
	    <div id="elmish-app"></div>
	    <!-- Polyfill for remotedev -->
	    <script type="text/javascript">
	        var global = global || window;
	    </script>
	    <script type="module" src="/output/App.js"></script>
	</body>
	</html>
	```
	
1. Run `npx tailwindcss init -p` in `src/Client`
1. Change `tailwind.config.js` content with

	```js
	/** @type {import('tailwindcss').Config} */
	module.exports = {
	    mode: "jit",
	    content: [
	        "./index.html",
	        "./**/*.{fs,js,ts,jsx,tsx}",
	    ],
	    theme: {
	        extend: {},
	    },
	    plugins: []
	}
	```

1. Create a file `src/Client/index.css` with the following content

	```css
	@tailwind base;
	@tailwind components;
	@tailwind utilities;
	```
	
1. Add the following lines at the top of `src/Client/App.fs`
	
	```fs
	open Fable.Core.JsInterop
	
	importSideEffects "./index.css"
	```

1. In `src/Client/Index.fs` replace all view code after the `update` function with the code [here](https://github.com/SAFE-Stack/SAFE-template/blob/master/Content/default/src/Client/Index.fs)

1. Create a file `tests/Client/vite.config.mts` with the following content:

	```ts
	import { defineConfig } from "vite";
	
	// https://vitejs.dev/config/
	export default defineConfig({
	    server: {
	        port: 8081
	    }
	});
	```
	
1. Overwrite `tests/Client/index.html` with 

	```html
	<!DOCTYPE html>
	<html>
	    <head>
	        <title>SAFE.App Client Tests</title>
	    </head>
	    <body>
	        <script type="module" src="/output/Client.Tests.js"></script>
	    </body>
	</html>
	```
	
1. Create `.fantomasignore` with the following content:
	
	```
	# Ignore Fable files
	src/Client/output/fable_modules/ 
	```

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

In order to get VSCode Tailwind intellisense, user needs to install `Tailwind CSS Intellisense`. We also create the file `.vscode/settings.json` with the following content.

	*The regex here are for Feliz style DSL, if you want to support Fable.React DSL you need to adapt the regexes*
	
	```json
	{
	    "tailwindCSS.includeLanguages": {
	        "fsharp": "html"
	    },
	    "tailwindCSS.experimental.classRegex": [
	        "\\.className\\s+\"([^\"]*)\"",
	        ["\\.className\\s+\\[([^\\]]*)\\]", "\"([^\"]*)\""]
	    ]
	}
	```