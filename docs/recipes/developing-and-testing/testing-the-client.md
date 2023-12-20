# How do I test the client?

Testing on the client is a little different than on the server.

This is because the code which is ultimately being executed in the browser is JavaScript, translated from F# by Fable, and so it must be tested in a JavaScript environment.

Furthermore, code that is shared between the Client and Server must be tested in both a dotnet environment _and_ a JavaScript environment.

The SAFE template uses a library called Fable.Mocha which allows us to run the same tests in both environments. It mirrors the Expecto API and works in much the same way.

## **I'm using the standard template**
****
If you are using the standard template then there is nothing more you need to do in order to start testing your Client.

In the tests/Client folder, there is a project named `Client.Tests` with a single script demonstrating how to use Mocha to test the TODO sample.

>Note the compiler directive here which makes sure that the Shared tests are only included when executing in a JavaScript (Fable) context. They are covered by Expecto under dotnet as you can see in `Server.Tests.fs`.

#### 1. Launch the test server

In order to run the tests, instead of starting your application using
```powershell
dotnet run
```
you should instead use
```powershell
dotnet run Runtests
```

#### 2. View the results

Once the build is complete and the website is running, navigate to `http://localhost:8081/` in a web browser. You should see a test results page that looks like this:

<img src="../../../img/mocha-results.png"/>

> This command builds and runs the Server test project too. If you want to run the Client tests alone, you can simply launch the test server using `npm run test:live`, which executes a command stored in `package.json`.

## **I'm using the minimal template**

If you are using the minimal template, you will need to first configure a test project as none are included.

#### 1. Add a test project
Create a `.Net` library called `Client.Tests` in the `tests/Client` subdirectory using the following commands:

```powershell
dotnet new classlib -lang F# -n Client.Tests -o tests/Client
dotnet sln add tests/Client
```

#### 2. Reference the Client project
Reference the Client project from the Client.Tests project:

```powershell
dotnet add tests/Client reference src/Client
```

#### 3. Add the Fable.Mocha package to Test project
Run the following command:

```powershell
dotnet add tests/Client package Fable.Mocha
```

#### 4. Add something to test

Add this function to Client.fs in the Client project

```fsharp
let sayHello name = $"Hello {name}"
```

#### 5. Add a test
Replace the contents of `tests/Client/Library.fs` with the following code:

```fsharp
module Tests

open Fable.Mocha

let client = testList "Client" [
    testCase "Hello received" <| fun _ ->
        let hello = Client.sayHello "SAFE V3"

        Expect.equal hello "Hello SAFE V3" "Unexpected greeting"
]

let all =
    testList "All"
        [
            client
        ]

[<EntryPoint>]
let main _ = Mocha.runTests all
```

#### 6. Add Test web page

Add a file called `index.html` to the tests/Client folder with following contents:
```html
<!DOCTYPE html>
<html>
    <head>
        <title>SAFE Client Tests</title>
    </head>
    <body>
        <script type="module" src="/output/Library.js"></script>
    </body>
</html>
```

#### 7. Add test webpack config

Add a file called `vite.config.mts` to the root directory with the following contents:

```
import { defineConfig } from "vite";

// https://vitejs.dev/config/
export default defineConfig({
    server: {
        port: 8081
    }
});

```

#### 8. Install the client's dependencies

```powershell
npm install
```

#### 9. Launch the test website

```powershell
cd tests/Client
dotnet fable watch -o output --run npx vite
```


Once the build is complete and the website is running, navigate to `http://localhost:8081/` in a web browser. You should see a test results page that looks like this:

<img src="../../../img/mocha-min-results.png"/>