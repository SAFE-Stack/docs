# How do I add shadcn to a SAFE project?

Integrating Feliz.Shadcn into your SAFE Stack application is straightforward. The following example demonstrates how to integrate Shadcn copmonents within an existing SAFE app.

We will be using the [Feliz.Shadcn](https://github.com/reaptor/feliz.shadcn) wrapper written by [reaptor](https://github.com/reaptor)

1. Setup Tailwind

> Note: When you use the SAFE template you will already have Tailwind installed by default. You can skip this step. 

Check out the following recipe here to install tailwind: [Add Tailwind](https://safe-stack.github.io/docs/recipes/ui/add-tailwind/)

1. Move `package.json` & `package-lock.json` to /src/Client

1. Configure import alias in tsconfig:

Create a file named tsconfig.json in `/src/Client` and add the following:

{
    "files": [],
    "compilerOptions": {
        "baseUrl": ".",
        "paths": {
            "@/*": [
                "./*"
            ]
        }
    }
}

1. Install shadcn/ui

> Note: ensure your node version is > 20.5.0

```
npx shadcn@latest init
```

You will be asked a few questions to configure components.json

1.  Add Feliz.Shadcn

Inside the `/src/Client` directory run:

```
dotnet add package Feliz.Shadcn
```

1. Start adding any shadcn component

Specify first which components you want to use.
You can find the list of available components [here](https://reaptor.github.io/Feliz.Shadcn/):

```
npx shadcn@latest add button 
```

1. Then use it in Feliz:

```fsharp
open Feliz.Shadcn


let view = 
    Shadcn.button [
        prop.text "Button" ]

```

Congratulations, now you can use shadcn components!
Documentation can be found at [https://reaptor.github.io/Feliz.Shadcn/](https://reaptor.github.io/Feliz.Shadcn/)
