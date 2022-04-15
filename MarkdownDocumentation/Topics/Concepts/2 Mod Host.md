Mod Host
========

Ultimate Modding Toolkit uses containers known as mod hosts to manage a single mod within the game which are represented as game objects. 

A mod host is essentially a dedicated manager for a specific mod and is responsible for the entire lifetime of that mod from creation until it is unloaded. There may be any number of mod hosts in the scene at any time unless `Allow Multiple Mods` has been disabled in the settings window, in which case only a single host can exist. Mod hosts may also be re-used to load a different mod and any subsequent calls to `LoadMod` will force the host to unload its current mod (if any). 

Mod hosts are known as state objects as they can be in many different states depending upon the operations performed on them. Each state is outlined below: 
- Unloaded (Default): The mod host contains no managing information about a specific mod. It is in a clean state and ready to take on management of a mod via a load call.
- Loading: The host is currently loading a mod and as such cannot perform operations such as load or unload.
- Loaded: The host has its assigned mod loaded and is currently managing it. 

These are the main states of a mod host and the following diagram shows how a host may transition to these states. Note that the `OnDestroy` event triggered by destroying the script or game object for the host will result in the host automatically unloading its current mod if one is loaded.
