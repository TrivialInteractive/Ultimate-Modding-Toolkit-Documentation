Unload Mod
==========

Once you have successfully implemented mod loading one of the next things you may want to do is unload mods which is also a trivial task. Mods may be unloaded at any time as you the developer sees fit and will cause the host to discard all mod related data resulting in a clean host instance that may be re-used.

> **_NOTE:_** _Due to the limitations of the CLR, any mod scripts that have been loaded into memory may remain after the host has unloaded the mod however, they will be treated as dead scripts and will not receive any events or context data from the host._

Unloading a mod is as simple as calling a single method as shown below:

```cs
using System;
using UnityEngine;
using UltimateModdingToolkit;

public class ModUnloadExample : MonoBehaviour
{
    private ModHost host = null;
    
    private void Start()
    {
        // Create a mod path from a string path
        Uri path = new Uri("file:///C:/Mods/TestMod.mod");
        
        // Issue a mod load request
        host = Mod.Load(path);
        
	if(host.IsLoaded == true)
        {
            // Unload the host when the load has successfully completed
            host.UnloadMod();
        }
    }
}
```

> **_NOTE:_** _If the host that you unload is a dependency of another mod host then that mod will also be unloaded to prevent the dependency chain from being broken._

You may already be aware that the mod host object derives from mono behaviour and as a result may be destroyed as you would normally destroy a Unity object. Destroying a host using `Object.Destroy` will indeed cause the managed mod to be unloaded as you might expect.
