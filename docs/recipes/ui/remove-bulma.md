# How do I remove Bulma from a SAFE project?

1. Remove / replace all the references to Bulma in fsharp code

1. Remove any stylesheet links to Bulma which may exist in the `index.html` page, or in other html pages.

1. Optional: If using Paket make sure Fable.Core is pinned.

    Edit `paket.dependencies`, set `Fable.Core < 4` - Fable 4 is not yet supported by SAFE.
    
    !!! warning "Dependency changes"
        When adding or removing dependencies, it is possible an upgraded version of an existing dependency can be incompatible and cause errors. Ensure important versions are pinned to avoid this.
    
    !!! info
        To avoid pinning a version when **adding** a dependency you can use the  `--keep-major` flag to make the upgrade more conservative.

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
