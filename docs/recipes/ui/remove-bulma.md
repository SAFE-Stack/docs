# How do I remove Bulma from a SAFE project?

1. Remove / replace all the references to Bulma in fsharp code

1. Remove any stylesheet links to Bulma which may exist in the `index.html` page, or in other html pages.

1. Optional: If using Paket ensure `Fable.Core` is set to a specified version.

    In `paket.dependencies`, make sure there is a line like so: `Fable.Core ~> 3`
    
    !!! warning
        SAFE is not yet compatible with newer versions of `Fable.Core`.  
        In the past, the version was not pinned so it was possible to accidentally upgrade to an incompatible version.
    
    !!! info
        To avoid specifying a version when **adding** a dependency - if it is not already pinned to a specific version - you can use the  `--keep-major` flag to make the upgrade more conservative.

1. Remove `Fulma` and `Feliz.Bulma`

    - Paket:
    ```bash
    dotnet paket remove Fulma
    dotnet paket remove Feliz.Bulma
    ```

    - NuGet:
    ```bash
    cd src/Client
    dotnet remove package Fulma
    dotnet remove package Feliz.Bulma
    ```
