Server-Side Rendering (SSR) means that some parts of your application code can run on both the server and the client.
For [React](https://reactjs.org/) this means that you can render your components directly to HTML on the server side (e.g. via a [node.js server](https://nodejs.org/en/)), which allows for better search engine optimization (SEO) and gives a faster initial response, especially on mobile devices.

The browser typically receives a static HTML site and starts updating the UI immediately;
React's bundle code will be downloaded asynchronously and when it completes, the client-side JavaScript will take over via [React's hydrate](https://reactjs.org/docs/react-dom.html#hydrate) functionality. In the JavaScript ecosystem this is also known as an "isomorphic" or "universal" app.

## Why use SSR?

### Pros

* Better SEO support, as web crawlers will directly see the fully rendered HTML page.
* Faster time-to-content, especially on slow internet connections or devices.

### Cons

* Some development constraints. Browser-specific code requires some compiler directives to be ignored when running on the server.
* Increased complexity of build and deployment processes.
* Increased server-side load.

## SSR on SAFE
In SAFE, SSR can be done using [fable-react](https://github.com/fable-compiler/fable-react). Its approach is a little different from those you might have seen in the JavaScript ecosystem, as it takes a purely F# approach: you render your [Elmish](https://github.com/elmish/elmish) views directly on .NET Core, with all the benefits of the .NET Core runtime.

## Further reading

* More details can be found in the [SSR tutorial](https://github.com/fable-compiler/fable-react/blob/master/docs/server-side-rendering.md).
* The [SAFE-BookStore](https://github.com/SAFE-Stack/SAFE-BookStore) sample project uses SSR.
