# How do I add a NuGet package to the Client?
Adding packages to the Client project is a very [similar process to the Server](../add-nuget-package-to-server), with a few key differences:

- Any references to the `Server` directory should be `Client`

- Client code written in F# is converted into JavaScript using [Fable](https://fable.io/docs/index.html). Because of this, we must be careful to only reference libraries which are [Fable compatible](https://fable.io/docs/your-fable-project/use-a-fable-library.html).

- If the NuGet package uses any JS libraries you must install them.  
  For simplicity, [use Femto to sync](./sync-nuget-and-npm-packages.md) - if the NuGet package is compatible - or [install via NPM](./add-npm-package-to-client.md) manually, if not.

There are [lots of great libraries](../../awesome-safe-components.md) available to choose from.