## What is Server-Side Rendering (SSR)?

Server-Side Rendering (SSR) means some part of your app's code can run on both the server and the client. 
For [React](https://reactjs.org/) this means that you can render your components to HTML on the server side (usually via a [node.js server](https://nodejs.org/en/)). This allows for better Search Engine Optimization and gives faster initial response, especially on mobile devices. The browser will receive a static HTML site and can start updateing the UI immediately.
React's bundle code will be downloaded asynchronously and when then download completes, the Client-Side JavaScript will take over via [React's hydrate](https://reactjs.org/docs/react-dom.html#hydrate) functionality.

In the JavaScript ecosystem this is also as known as "isomorphic app" or "universal app".

In SAFE stack Server-Side Rendering can be done with [fable-react](https://github.com/fable-compiler/fable-react).

### Why using SSR?

#### Pros

* Better Search Engine Optimization (SEO), as the web crawlers will directly see the fully rendered HTML page
* Faster time-to-content, especially on slow internet or slow devices

#### Cons

* Some development constraints. Browser-specific code needs compiler directives to be ignored when running on the server.
* Build and deployment processes will become more complicated.
* More server-side load.