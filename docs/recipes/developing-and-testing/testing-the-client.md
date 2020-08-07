# How do I test the client?

Testing on the client is a little different than on the server.

This is because the code which is ultimately being executed in the browser is Javascript, once the F# that you write is translated by Fable, and so must be tested in a Javascript environment.

Furthermore, and code that is shared between the Client and Server must be test in both a dotnet environment _and_ a Javascript environment.

The SAFE template uses a library called Fable.Mocha which allows us to run the same tests in both environments. It mirrors the Expecto API and works in much the same way.


## **I'm using the standard template**

If you are using the standard template then there is nothing more you need to do in order to get started testing your Client.

You will find a folder in the solution named **tests**. Inside this there is a project, **Client.Tests**, which contains a single script demonstrating how to use Mocha to test the TODO sample.

>Note the compiler directive here which makes sure that the Shared tests are only included when executing in a Javascript (Fable) context. They are covered by Expecto under dotnet as you can see in **Server.Tests.fs**.

####1. Launch the test server

In order to run the tests, instead of starting your application using
```powershell
dotnet fake build -t run
```
you should instead use
```powershell
dotnet fake build -t runtests
```

####2. View the results

Once the build is complete and the website is running, navigate to `http://localhost:8081/` in a web browser. You should see a test results page that looks like this:

<img src="../../../img/mocha-results.png"/>

> This command builds and runs the Server test project too. If you want to run the Client tests alone, you can simply navigate to the Client directory and launch the test server using `npm run test:live`, which executes a command stored in `package.json`.


