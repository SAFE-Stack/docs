# How do I test the client?

Testing on the client is a little different than on the server.

This is because the code which is ultimately being executed in the browser is Javascript, translated from F# by Fable, and so it must be tested in a Javascript environment.

Furthermore, code that is shared between the Client and Server must be tested in both a dotnet environment _and_ a Javascript environment.

The SAFE template uses a library called Fable.Mocha which allows us to run the same tests in both environments. It mirrors the Expecto API and works in much the same way.

## **I'm using the standard template**
****
If you are using the standard template then there is nothing more you need to do in order to start testing your Client.

You will find a folder in the solution named **tests**. Inside this there is a project, **Client.Tests**, which contains a single script demonstrating how to use Mocha to test the TODO sample.

>Note the compiler directive here which makes sure that the Shared tests are only included when executing in a Javascript (Fable) context. They are covered by Expecto under dotnet as you can see in **Server.Tests.fs**.

#### 1. Launch the test server

In order to run the tests, instead of starting your application using
```powershell
dotnet run
```
you should instead use
```powershell
dotnet run RunTests
```

#### 2. View the results

Once the build is complete and the website is running, navigate to `http://localhost:8081/` in a web browser. You should see a test results page that looks like this:

<img src="../../../img/mocha-results.png"/>

> This command builds and runs the Server test project too. If you want to run the Client tests alone, you can simply launch the test server using `npm run test:live`, which executes a command stored in `package.json`.

## **I'm using the minimal template**

If you are using the minimal template, you will need to first configure a test project as none are included.

#### 1. Add a test project
In the `src` folder, create a create a **.Net** library called **Client.Tests**.

```powershell
cd src
dotnet new ClassLib -lang F# -o Client.Tests
cd ..
dotnet sln add src/Client.Tests
```

#### 2. Reference the Client project
Reference the Client project from the Client.Tests project:

```powershell
dotnet add Client.Tests reference Client
```

#### 3. Add the Fable.Mocha package to Test project
Run the following command:

```powershell
dotnet add Client.Tests package Fable.Mocha
```

#### 4. Add a test
Delete the Library.fs file and replace it with a new file called `Tests.fs` to your test project. Add the following code to it:
```fsharp
module Tests

open Fable.Mocha
open Index

let client = testList "Client" [
    testCase "Hello received" <| fun _ ->
        let hello = "Hello Test!"
        let model, _ = init ()
        let model, _ = update (GotHello hello) model

        Expect.equal hello model.Hello "The hello should be set"
]

let all =
    testList "All"
        [
            client
        ]

[<EntryPoint>]
let main _ = Mocha.runTests all
```

#### 5. Add Test web page

Add a file called **index.html** to the root of the test project and add the following content to it:
```html
<!DOCTYPE html>
<html>
    <head>
        <title>SAFE Client Tests</title>
    </head>
    <body>
    </body>
</html>
```

#### 6. Add test webpack config

- Add a new file root directory called **webpack.tests.config.js**.

- Populate it with the contents of a Fable-compatible webpack config template [such as this](https://github.com/fable-compiler/webpack-config-template/blob/master/webpack.config.js).

#### 7. Update the test webpack config

- Replace the `CONFIG` value in the webpack file you just created with the following:
```fsharp
var CONFIG = {
    indexHtmlTemplate: './src/Client.Tests/index.html',
    fsharpEntry: './src/Client.Tests/Client.Tests.fsproj',
    outputDir: './src/Client.Tests',
    assetsDir: './src/Client.Tests',
    devServerPort: 8081,
    devServerProxy: undefined,
    babel: undefined
}
```

- Remove all references and code from the webpack file that refers to MiniCssExtractPlugin or CONFIG.cssEntry.

#### 8. Add launch command

Add the following entry to the `scripts` element in the Client project's `package.json` file:

```json
"test:live": "webpack-dev-server --config webpack.tests.config.js"
```

#### 9. Build the Tests

Save all changes and build the Client.Test project.

####9. Launch the test website

Run the command

```powershell
npm install
```
followed by
```powershell
npm run test:live
```

Once the build is complete and the website is running, navigate to `http://localhost:8081/` in a web browser. You should see a test results page that looks like this:

<img src="../../../img/mocha-min-results.png"/>
