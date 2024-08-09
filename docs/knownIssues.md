# Known Issues

This page contains a (hopefully short) list of higher impact bugs that affect the SAFE template. It is not our intention to make an exhaustive list here; for all issues, check [GitHub](https://github.com/SAFE-Stack/SAFE-template/issues).

## Server not starting: error: "dotnet paket restore" exited with code -532462766 

.NET SDK 8.0.300 introduced a bug that caused the Server startup to fail with a paket error (`"dotnet paket restore" exited with code -532462766`). This has been resolved in SDK version 8.0.303.

If for some reason you can not use a newer SDK version, pin the SDK version using`global.json`:

```
{
    "sdk": {
    "version": "8.0.206"
    }
}
```