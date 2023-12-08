# How do I remove Bulma from a SAFE project?

1. Remove / replace all the references to Bulma in fsharp code

1. Remove any stylesheet links to Bulma which may exist in the `index.html` page, or in other html pages.

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
