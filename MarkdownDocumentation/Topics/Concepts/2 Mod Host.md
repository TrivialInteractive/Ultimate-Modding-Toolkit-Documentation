Mod Host
========

Ultimate Modding Toolkit uses containers known as mod hosts to manage a single mod within the game which are represented as game objects. 

A mod host is essentially a dedicated manager for a specific mod and is responsible for the entire lifetime of that mod from creation until it is unloaded. There may be any number of mod hosts in the scene at any time unless `Allow Multiple Mods` has been disabled in the settings window, in which case only a single host can exist.

Mod hosts are known as state objects as they can be in many different states depending upon the operations performed on them. Each state is outlined below: 
- **Unloaded (Default):** The mod host contains no managing information about a specific mod. It is in a clean state and ready to take on management of a mod via a load call.
- **Loading:** The host is currently loading a mod and as such cannot perform operations such as load or unload.
- **Loaded:** The host has its assigned mod loaded and is currently managing it. 

These are the main states of a mod host and the following diagram shows how a host may transition to these states. Note that the `OnDestroy` event triggered by destroying the script or game object for the host will result in the host automatically unloading its current mod if one is loaded.

![](https://github.com/TrivialInteractive/Ultimate-Modding-Toolkit-Documentation/blob/main/MarkdownDocumentation/Images/Concepts/Mod%20Host%20Lifecycle.png?raw=true)

> **_NOTE:_** _Mod hosts are recyclable objects and may be reclaimed if they are in their unloaded state as a direct result of a new host request. If you want to manage hosts manually and ensure that they are not `collected` then you should set the `ModHost.CanBeReclaimed` property on that specific host to false._

Mod hosts are implemented as unity components and as such can be attached to a game object in order to create an instance. While this is a valid method of creating a host, we recommend that hosts are created via the static `CreateNewHost` method which will recycle any idle hosts as well as creating a dedicated object for the host and its sub systems. 

The following code shows how a new host can be created via script:

```cs
using UltimateModdingToolkit;
using UMod;

public class ModHostExample : MonoBehaviour
{
    private void Start()
    {
        // Create the mod host using the static method
        ModHost host = ModHost.CreateNewHost();
    }
}
```

> **_NOTE:_** _Mod host creation will usually be managed by a load or async load request meaning that it is not necessary to create hosts manually unless you need more control over the load process._