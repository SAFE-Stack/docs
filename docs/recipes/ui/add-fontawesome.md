# How Do I Use FontAwesome?
[FontAwesome](https://fontawesome.com/) is the most popular icon set out there and will provide you a handful of free icons as well as a multitude of premium icons. The standard SAFE template has out-of-the-box support for FontAwesome. You can just start using it in your Client code like so:
```fsharp
open Fable.FontAwesome

Icon.icon [
    Fa.i [ Fa.Solid.Star ] [ ]
]
```
This will display a solid star icon.

## I am Using the Minimal Template
If you’re using the minimal template, there are a couple of things to do before you can start using FontAwesome.

#### 1. The NuGet Package
Add [Fable.FontAwesome.Free NuGet Package](https://www.nuget.org/packages/Fable.FontAwesome.Free/) to the Client project.
> See [How do I add a Nuget package to the Client?](../package-management/add-nuget-package-to-client.md).

#### 2. The CDN Link
Open the `index.html` file and add the following line to the `head` element:
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.14.0/css/all.min.css">
```

#### All Done!
Now you can use FontAwesome in your code