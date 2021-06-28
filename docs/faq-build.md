This page explains the key differences that you should be aware of between running SAFE applications in development and production.

## Developing SAFE applications
The SAFE template is geared towards a streamlined development process. It builds and runs both the client and server on your machine.

During development, in parallel to your .NET web server, Webpack Dev Server is used to enable hot module replacement. This means that you can continually make changes to your client application code and can rapidly see the results reflected in your browser, without the need to fully reload the application.

The build of the .NET server also makes use of dotnet watch to have the server automatically restart with the latest changes. Since your backend applications will typically be stateless, this permits a rapid development workflow.

It's important to note that the webpack dev server is configured to automatically route traffic intended for api/* routes to the backend web server. This simulates how a SAFE application might work in a production environment, with both client and server assets served from a single web server. This also allows you to not worry about ports and hosts for your backend server in your client code, or CORS issues.

```mermaid
flowchart LR
subgraph c[localhost:8080]
js>Fable-compiled JS]
webpack(Webpack dev server)
js -- hot module replacement --- webpack
end
subgraph s[localhost:8085]
dotnet(dotnet watch run)
saturn(Saturn on Kestrel)
saturn --- dotnet
end
c -- /api redirect --> s
```

# Running SAFE applications in production
In a production environment, you won't need the Webpack Dev server. Instead, Webpack is used as a one-off compiler step to create your bundled Javascript from your Fable app (plus dependencies), and then deploy this along with your backend web server which also hosts that content directly. For example, you can use Saturn to host the static content required by the application e.g. HTML, JS and CSS files etc. as well as your backend APIs. This fits very well with standard CI / CD processes, as a build step in your Build.fs or Azure DevOps / AppVeyor / Travis step etc.

```mermaid
flowchart BT
subgraph dest[Web server e.g. https://contoso.com]
saturn(Saturn myapp.dll)
db[(transactional data)]
assets>static assets]
saturn -- api/customers --- db
saturn -- bundle.js --- assets
end

subgraph src[CI/CD Server]
exec>deployment script]
webpack(webpack)
dotnet(dotnet publish)
source(F# source code)
exec -- bundle.js --- webpack
exec -- myapp.dll --- dotnet
webpack --- source
dotnet --- source
end

src -- file copy --> dest

```

### Client asset hosting alternatives

Rather than hosting your client-side content and application inside your web server, you can opt to host your static content from some other service that supports hosting of HTTP content, such as Azure Blobs, or a content hosting service. In such a case, you'll need to consider how to route traffic to your back-end API from your client application (as they are hosted on different domains), as well as handle any potential CORS issues.