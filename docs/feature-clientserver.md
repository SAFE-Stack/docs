One of the most powerful features of SAFE is the ability to seamlessly share code across client and server.

## Sharing Basics
The basics of code sharing across client and server include:

* Sharing **types**. Useful for contracts between client and server, as well as to share a common domain
* Sharing **behaviour**. In other words, functions that perform e.g. shared validation or similar.

These areas is explained in more detail [here](feature-clientserver-basics.md).

## Sending messages between client and server

There are several technologies availabile for client / server communications in SAFE applications. Each has their own strengths and weaknesses:

* [Raw HTTP](feature-clientserver-http.md) using Saturn's routing capabilities.
* [Contracts / protocols](feature-clientserver-remoting.md) via Fable Remoting.
* [Stateful servers](feature-clientserver-bridge.md) through Elmish Bridge.

## Which technology should I use?
The raw HTTP model provided by Saturn with `router { }` requires you to construct routes manually and does not guarantee that the client and endpoint have the same contract (you have to specify the same type on both sides yourself). However, using the raw HTTP model gives you total control over the routing and verbs used. If you have a public API that is exposed not just to your own application but to third-parties, or you need more fine grained control over your routes and data, you should use this approach.

Alternatively, Fable Remoting provides an excellent way to quickly get up and running with the SAFE stack. You can rapidly create contracts and have guaranteed type-safety between both client and server. Considor using remoting for rapid prototyping, since JSON serialization and HTTP routing is handled by the library, you only think of your client-server code in terms of types and stateless functions. If you need full control over the HTTP channel for returning specific status codes, using custom HTTP verbs or working with headers, then remoting is probably not for you. 

Lastly, Elmish Bridge provides an alternative way of modelling client/server communication. Unlike the other two mechanisms, Elmish Bridge provides the same Elmish model on the server as well as the client, as well as the ability to send notifications from the server back to connected clients via websockets. However, the Bridge model is inherently stateful, which means that a server restart could impact all connected clients.

| | Fable.Remoting | Raw HTTP | Elmish.Bridge |
|-|:-:|:-:|:-:|
| Client / Server support | Very easy | Easy | Very Easy |
| State model | Stateless | Stateless | Stateful |
| "Open" API? | Yes | Yes | No |
| HTTP Verbs? | POST, GET | Fully Configurable | None |
| Push messages? | No | No | Yes |
| Pipeline Control? | Limited | Full | Limited |

Consider using a combination of multiple endpoints supporting combinations of the above to suit your needs!