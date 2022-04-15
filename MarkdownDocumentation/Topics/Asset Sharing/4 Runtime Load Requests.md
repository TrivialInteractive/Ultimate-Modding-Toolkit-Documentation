Runtime Load Requests
=====================

The main purpose of the shared assets database is for relinking game assets used in loaded mods at runtime. As mentioned previously, this means that game assets may need to be loaded dynamically if they are used inside a mod. This process is handled automatically, usually during the mod loading process, but it can also occur on demand. 

Any mods that make use of shared game assets will be processed at build time to store the asset guid information only. As you may have guessed, that guid information corresponds to a specific shared asset database entry, which is loaded when required using the Unity Resources API. This load request will be immediate if the referenced game asset is already loaded by Unity, and will be awaitable if using `Mod.LoadAsync`. Load requests are also reference counted so that database entry asset can be unloaded when it is no longer used to save valuable memory. The target game asset may remain in memory if it is used in other aspects of the game.
