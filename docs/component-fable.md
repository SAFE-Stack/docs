# [Fable](http://fable.io/) in SAFE

## What is Fable?

Fable is an F#-to-JavaScript (JS) compiler powered by [Babel](https://babeljs.io/), designed to produce readable and standard JS code. Fable brings all the power of F# to the JS ecosystem, with support for most of the F# core library as well the most commonly used .NET APIs.

## How does Fable integrate with SAFE?
Fable is much more than an F#-to-JS compiler - it also provides *rich* integration with the JS ecosystem which means that you can use JS libraries from F# (and vice versa) as well as make use of standard JS tools.

It's important to understand the Fable is not simply an application that takes in F# and emits JS - instead, the dotnet fable application emits an intermediary file (a Babel Abstract Syntax Tree), which in turn is converted by webpack into well-written JS.

## Fable and webpack
Fable is actually not a .NET Core tool, but is an NPM package that is hosted within the context of [webpack](https://webpack.js.org/), a powerful bundling tool. You'll normally see a `webpack.config.js` file in the client folder of your SAFE applications. This file tells webpack how to emit JS from F# files and hosts the Fable webpack plugin, `fable-loader`.

Using webpack also provides many advantages - for example, we as developers can control how JS is rendered through standard tools that exist in the JS ecosystem, whilst also using features such as [Hot Module replacement](feature-hmr.md) and Source Maps.

Creating a webpack config file isn't the easiest thing in the world, so the [SAFE Template](template-overview.md) already has one pre-built that contains the basics to get you up and running immediately.

Learn more about Fable [here](http://fable.io/).