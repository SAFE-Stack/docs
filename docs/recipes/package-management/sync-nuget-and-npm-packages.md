# How do I ensure NPM and Nuget packages stay in sync?
SAFE Stack uses Fable bindings, which are NuGet packages that provide idiomatic and type-safe wrappers around native JavaScript APIs. One type of these bindings is third-party JavaScript libraries distributed to the NPM registry, which gives birth to the problem of keeping them in sync with their corresponding NuGet packages. [Femto](https://github.com/Zaid-Ajaj/Femto) is a handy dotnet CLI tool that solves this issue. 

> For in-depth information about Femto, see [Introducing Femto](https://fable.io/blog/Introducing-Femto.html).

#### 1. Install Femto
You can install Femto either as a global tool, or a local tool that only exists for a certain project.
##### 1a. Install as A Global Tool
Execute the following command:
```powershell
dotnet tool install femto --global
```
##### 1b. Install as A Local Tool
Navigate to the root folder of the solution and execute the following command:
```powershell
dotnet tool install femto
```

---

> Note that if you installed femto as a local tool, you will need to type “dotnet femto” instead of just “femto” for the following instructions.

---

#### 2. Analyse Dependencies
Navigate to `src/Client` and run the following:
```powershell
femto
```
This will give you a report of discrepancies between the NuGet packages and the NPM packages for the project, as well as steps to take in order to resolve them.

#### 3. Resolve Dependencies
To sync your NPM dependencies with your NuGet dependencies, you can either manually follow the steps returned by *step 2*, or resolve them manually using the following command:
```powershell
femto --resolve
```

## Done!
Keeping your NPM dependencies in sync with your NuGet packages is now as easy as repeating step 3. Of course you can instead repeat the step 2 and resolve packages manually, too.