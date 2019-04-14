# News and Announcements
## 2019
### 5th April
We're delighted to announce the release of v1.0 of the SAFE Stack! It's been an exciting year or so for the stack as the stack has evolved and matured. We now have what we feel is a stable and feature-rich stack that achieves the original goals of SAFE, plus a number of optional libraries that people can take advantage of.

With the release of v1, you can now do the following:

* Create a fully working, full-stack web application in F# using the SAFE template in just a few seconds and have it running on your machine from clean in less than a minute.
* Choose flexible hosting options, including Azure App Service, Google Cloud Platform and Docker.
* Host with multiple server models including Saturn and Giraffe.
* Have simultaneous client and server-side debugging sessions in VS Code.

You can upgrade the SAFE Stack template via the dotnet CLI: `dotnet new -i SAFE.Template` to get the latest and greatest version, or `dotnet new -i SAFE.Template::1.0` to pin to v1.0.

We're looking forward to continuing to evolve the SAFE Stack in the coming months. We have a lot of great ideas for the future, and will be publishing our vNext roadmap in the coming weeks. As always, we're interested in your feedback and ideas, so please reach out to us via the [support](support.md) page.

## 2018
### 5th August
We're pleased to see that the Suave team have clarified their license and explicitly removed the dependency on the Logary package. However, our decision to remove Suave from the SAFE stack remains: **Suave no longer forms a part of the strategic goals of the SAFE project**, and our server-side focus remains on improving the experience for both Giraffe and Saturn.

We nonetheless wish the Suave project, team and contributors the best of luck for the future.

### 18th June
Due to the unclear future regarding the licensing of Suave and its dependencies, the SAFE team have today made the unamimous decision to **remove Suave as a recommended option on the SAFE stack**. We will no longer provide guidance on integrating Suave with the SAFE stack, nor will we maintain existing capabilities for it in SAFE tooling.

**Our default recommendation for SAFE stack applications is to use Saturn or Giraffe directly, running on top of Kestel on ASP .NET.**

SAFE will continue to promote all libraries, frameworks and toolchains that provide clear and consistent licensing, do not aim to discriminate against specific libraries on a commercial basis and promote open discussion.
