Custom Metadata
===============

A built mod package contains standard information such as mod name, version author and more, as well as things like an icon for the mod. This data is standard to all Ultimate Modding Toolkit mods and can be accessed using the `IModInfo` API at runtime or edit time. The main benefit to this metadata is that it can be accessed very quickly by partially loading the mod file header (`Mod.Load`) is not required to access such information). 

In some cases, it would be useful to store additional information in a similar way so that full mod loading is not required in order to access it. This is where custom metadata comes in. This feature allows you to optionally include any amount of custom data in an easy way. 

[Continue (Define Custom Metadata)](2 Define Custom Metadata.md)