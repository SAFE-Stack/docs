# How do I add shadcn to a SAFE project?

> Note: The SAFE.Template has recently been updated to move `package.json` and `package-lock.json`. If you are using a version of the template prior to this change, you will need to move those files into the `src/Client` directory. For reference, here is the [PR](https://github.com/SAFE-Stack/SAFE-template/pull/658) detailing this change.

Integrating Feliz.Shadcn into your SAFE Stack application is straightforward. The following example demonstrates how to integrate Shadcn copmonents within an existing SAFE app.

We will be using the [Feliz.Shadcn](https://github.com/reaptor/feliz.shadcn) wrapper written by [reaptor](https://github.com/reaptor)

#### 1. Setup Tailwind
> Note: When you use the SAFE template you will already have Tailwind installed by default. You can skip this step.

Check out the following recipe here to install Tailwind: [Add Tailwind](https://safe-stack.github.io/docs/recipes/ui/add-tailwind/)

#### 2. Configure import alias in tsconfig:

Create a file named `tsconfig.json` in `/src/Client` and add the following:

```json
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
```

#### 3. Update the `vite.config.mts` within `/src/Client`

Add the `resolve` property below under the `defineConfig`

```
export default defineConfig({
    ...
    resolve: {
        alias: {
            "@": path.resolve(__dirname),
        },
    },
    ...
});
```

#### 4. Install shadcn/ui

> Note: ensure your node version is > 20.5.0

Inside the `/src/Client` directory run:
```
npx shadcn@latest init
```

You will be asked a few questions to configure components.json

#### 5. Add Feliz.Shadcn

Inside the `/src/Client` directory run:
```
dotnet add package Feliz.Shadcn
```

#### 6. Start adding any shadcn component

Specify first which components you want to use.
You can find the list of available components [here](https://reaptor.github.io/Feliz.Shadcn/):

Inside the `/src/Client` directory run:
```
npx shadcn@latest add button
```

#### 7. Use it in Feliz:

```fsharp
open Feliz.Shadcn


let view =
    Shadcn.button [
        prop.text "Button" ]

```

Congratulations, now you can use shadcn components!
Further documentation can be found at [https://reaptor.github.io/Feliz.Shadcn/](https://reaptor.github.io/Feliz.Shadcn/)
