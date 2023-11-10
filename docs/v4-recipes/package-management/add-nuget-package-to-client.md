# How do I add a Nuget package to the Client?
Adding packages to the Client project is a very [similar process to the Server](../add-nuget-package-to-server), with one important difference: Although the Client code is written in F#, at runtime it is converted into Javascript using [Fable](https://fable.io/docs/index.html). Because of this, we must be careful to only reference libraries which are [Fable compatible](https://fable.io/docs/your-fable-project/use-a-fable-library.html).

There are [lots of great libraries](../../awesome-safe-components.md) available to choose from.