# How do I ensure NPM and Nuget packages stay in sync?
SAFE Stack uses Fable bindings, which are NuGet packages that provide idiomatic and type-safe wrappers around native JavaScript APIs. These bindings often rely on third-party JavaScript libraries distributed via the NPM registry. This leads to the problem of keeping both the NPM package in sync with its corresponding NuGet F# wrapper. [Femto](https://github.com/Zaid-Ajaj/Femto) is a dotnet CLI tool that solves this issue.

> For in-depth information about Femto, see [Introducing Femto](https://fable.io/blog/Introducing-Femto.html).

---

#### 1. Install Femto
Navigate to the root folder of the solution and execute the following command:
```powershell
dotnet tool install femto
```

#### 2. Analyse Dependencies
In the root directory, run the following:
```powershell
dotnet femto
```
This will give you a report of discrepancies between the NuGet packages and the NPM packages for the project, as well as steps to take in order to resolve them.

#### 3. Resolve Dependencies
To sync your NPM dependencies with your NuGet dependencies, you can either manually follow the steps returned by *step 2*, or resolve them automatically using the following command:
```powershell
dotnet femto --resolve
```

## Done!
Keeping your NPM dependencies in sync with your NuGet packages is now as easy as repeating step 3. Of course, you can instead repeat the step 2 and resolve packages manually, too.