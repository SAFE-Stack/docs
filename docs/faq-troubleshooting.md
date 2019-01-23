## Getting Started

### FAKE cannot be found

If you fail to execute `fake` from command line after installing it as a global tool, you might need to add it to your `PATH` manually: (e.g. `export PATH="$HOME/.dotnet/tools:$PATH"` on unix) - [related GitHub issue](https://github.com/dotnet/cli/issues/9321)

## Diagnostics

### SocketProtocolError in Debug Console

You may see the following `SocketProtocolError` message in the Debug Console once you have started your SAFE application.

> `WebSocket connection to 'ws://localhost:8000/socketcluster/' failed: Error during WebSocket handshake: Unexpected response code: 404`

<center><img src="../img/feature-debugging-5.png" style="height: 175px;"/></center>

Whilst these messages can be safely ignored, you can eliminate them by installing **Redux Dev Tools** in the launched Chrome instance as described in the debugging [prerequisites](feature-debugging.md#prerequisites) section.

## Debugging in VS Code

### Node Process remains after stopping the debugger

VS Code does not kill the Fable dotnet process when you stop the debugger, leaving it running as a "zombie". In such a case, you will have to explicitly kill the process otherwise it will hold onto
port 8080 and prevent you starting new instances. Tracked [here](https://github.com/SAFE-Stack/SAFE-template/issues/191).

### Chrome opens to a blank window

* Occasionally, VS Code will open Chrome before the Client has started. In this case, you will be presented with a blank screen until the client starts.
* Depending on the order in which compilation occurs, VS Code may launch the web browser before the server has started. If this occurs, you may need to refresh the browser once the server is fully initialised.

## Fable

### JavaScript bundle size

A project created from SAFE template might issue following warning from Webpack upon building the JavaScript bundle:

```
WARNING in entrypoint size limit: The following entrypoint(s) combined asset size exceeds the recommended limit (244 KiB). This can impact web performance.
```

We're striving to optimise the bundle size, however with a number of different options and dependencies it's not that easy to stay below the Webpack recommended limit.

To minimize the bundle size in your project you can try restricting browser compatibility by modifying the [Babel Preset targets for Browserslist](https://babeljs.io/docs/en/babel-preset-env#targets) and thus using less polyfills.

For more info, see this [issue](https://github.com/SAFE-Stack/SAFE-template/issues/185).
