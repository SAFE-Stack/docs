# [Fable](http://fable.io/) in SAFE

## What is Fable?


Fable is an F#-to-JavaScript (JS) compiler, designed to produce readable and standard JS code. Fable brings all the power of F# to the JS ecosystem, with support for most of the F# core library as well as the most commonly used .NET APIs.

It also provides *rich* integration with the JS ecosystem which means that you can use JS libraries from F# (and vice versa) as well as make use of standard JS tools.

## How does Fable integrate with SAFE?

Fable is a .NET tool that generates JavaScript files directly this allows us to write full front end applications using F#. Being able to write both the Server and Client in the same language offers huge benefits especially when you can share code between the two, without the need for duplication. More information on code sharing can be found [here](./feature-clientserver).

## Fable and webpack

As Fable allows us to integrate into the JS Ecosystem it allows us to use webpack and features like [Hot Module replacement](feature-hmr.md) and Source Maps.

Creating a webpack config file isn't the easiest thing in the world, so the [SAFE Template](template-overview.md) already has one pre-built that contains the basics to get you up and running immediately.

Learn more about Fable [here](http://fable.io/).