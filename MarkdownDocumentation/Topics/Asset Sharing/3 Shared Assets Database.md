Shared Assets Database
======================

In order for the asset sharing feature to work correctly, Ultimate Modding Toolkit needs to be able to load any game asset dependencies during the mod loading period, which the shared assets database is used for. For example: If a mod uses a building prefab from the game via asset sharing, it will have a dependency on this corresponding game asset which will be setup at build time. This dependency will likely need to be resolved at some point if the mod scene is ever loaded. By default, Ultimate Modding Toolkit will try to preload these shared game assets (only assets that are not already loaded by the game will be loaded) during the loading period. This allows the loading process to be blended seamlessly with the mod loading progress, so that calls like `Mod.LoadAsync` will also wait for game asset dependencies to be loaded.

You can disable the pre-loading of shared game assets when a mod is loaded via the settings window if required. Open the settings menu:

`Tools -> Ultimate Modding Toolkit -> Settings`

And find the setting named `Pre-Load Shared Game Assets`. Disable this option as required, however it is recommended that it remains enabled for smoother experiences with larger mods.

![](https://github.com/TrivialInteractive/Ultimate-Modding-Toolkit-Documentation/blob/main/MarkdownDocumentation/Images/Asset%20Sharing/Mod%20Settings%20-%20Shared%20Assets.png?raw=true)

Disabling this option will force any referenced shared game assets to be loaded on demand as required. Typically, this will occur immediately after a mod scene has been loaded.

> **_NOTE:_** _If a large number of unloaded game assets are referenced by a mod, it may take some time for all of these assets to be loaded on demand, potentially causing frame drops and stuttering._

The shared assets database is used at runtime to load and unload shared game assets as required by the current loaded mods. This process is completely automatic and should not need any interaction, but it may be useful to know how it works so that you can better understand the asset sharing system.

The shared assets database is essentially a collection of lightweight data assets that store a reference to a single game asset. There should always be a database entry for each shared game asset, and it will always be named with the corresponding assets guid information. You can find the shared assets database under the following folder once it has been generated:

`Assets/UltimateModdingToolkit/DataGenerated/Resources/SharedAssetsDatabase`

A typical generated shared assets database might look like the following:

![](https://github.com/TrivialInteractive/Ultimate-Modding-Toolkit-Documentation/blob/main/MarkdownDocumentation/Images/Asset%20Sharing/Shared%20Assets%20Database%20Example.png?raw=true)


Generating Shared Assets Database
---------------------------------

As mentioned previously, the shared assets database is created and maintained automatically by Ultimate Modding Tookit. The database will usually be created or updated when the mod tools are built or applied in project. 

If you are experiencing errors or problems relating to asset sharing, it may be related to assets being deleted or created without updating the shared assets database. In these cases, you will usually want to rebuild your mod tools package, but you can also update the database manually if needed. You can force the shared assets database to be rebuilt from the following menu:

`Tools -> Ultimate Modding Toolkit -> Project -> Generate Shared Assets Database`

You can also force the shared assets database to be cleared completely so that you can start fresh using the following menu:

`Tools -> Ultimate Modding Toolkit -> Project -> Clear Shared Assets Database`

> **_NOTE:_** _Before performing any of these actions, it is highly recommended that you create a project backup as a precaution. It is good practice, especially since the clear command will be deleting assets from the project._

