## Server-side options in SAFE

The [SAFE template](safe-template) comes with two alternative web server technologies out of the box:

* **Saturn** - A simple, flexible F# abstraction layer that runs on top of Giraffe that enables both MVC- and Web API-style services. 
* **Giraffe** - A flexible F# framework for creating web-enabled applications. Giraffe runs on top of ASP .NET Core and the Kestrel server.

![](img/safe-server-1.png)

Both Saturn and Giraffe are **fully ASP .NET Core compatible** and offer **F#-first** programming experiences - there's no need to wrap your DTOs in `[<CLIMutable>]` or inherit from base classes to get up and running.

## Why Saturn?
** Saturn is the default and recommended web server in SAFE**.

Saturn (and Giraffe) runs on top of ASP .NET Core, which comes with an extremely high-performance server that is fully supported by Microsoft, has a huge community of developers and has excellent integration into cloud services such as Microsoft Azure and Amazon Web Services.

In addition, whilst the Giraffe programming is extremely composable and fully functional-first, Saturn allows you to take advantage of abstractions such as `application { }`, `scope { }` and `controller { }` which are not only extremely simple to use but will also be familiar to developers from other (non-.NET) web programming models.

It's important to note that Saturn lives "on top" of Giraffe; you can think of Saturn as a layer of added goodness which provide a set of new higher-level abstractions which delegate down to Giraffe's HTTP Handler. And since Saturn's abstractions are all *optional*, you can always fall back directly to the lower-level Giraffe model if required.

## Coming from Suave?
Giraffe's HTTP Handler is a very similar abstraction to that of the Suave web server, which means that you can easily migrate over to Giraffe (and Saturn) and quickly start to benefit from the features that ASP .NET, Giraffe and Saturn all provide.