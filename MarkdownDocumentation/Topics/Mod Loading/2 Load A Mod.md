Load Mod
========

Loading a mod using Ultimate Modding Toolkit is relatively straight forward and can be achieved using a single method call, we do however need to know the location of the mod before we can load it. Ultimate Modding Toolkit is able to load mods from both local and remote locations and will determine the location of the path during loading. 

> **_NOTE:_** _If mods are located on a remote server then the loading time can drastically increase since the mod must first be downloaded. The actual time will depend on a few factors such as the size of the mod and the download speed but you are able to retrieve download statistics and progress while loading._

The following code shows how a mod can be loaded using a local mod path:

```cs
using System;
using UnityEngine;
using UltimateModdingToolkit;

public class ModLoadExample : MonoBehaviour
{
    private void Start()
    {
        // Create a uri object from a string path
        Uri path = new Uri("file:///C:/Mods/TestMod.mod");
        
        // Issue a mod load request
        Mod.Load(path);
    }
}
```

> **_NOTE:_** _An important thing to note when specifying a mod path is that you must include the full file name with extension._

Check For Loading Errors
------------------------

It is very easy to issue a mod load request as you have seen but how do we actually know that the mod loaded successfully? It is good practice to implement some error checking code and to not assume that the mod will always load correctly without errors. Ultimate Modding Toolkit aims to be as simple and intuitive to use and as a result you can check whether a load failed by accessing a single property. Of course, you can also get much more information about the loading error if needed to help with debugging or error reporting.

First off, you may have already noticed that the return value of the load method is a mod host which is essentially a manager object that is responsible for managing the loaded mod. For detailed information take a look at the Mod Host section. The host is a state object and has a number of variables that represent the current state of the host, such as: `IsLoading` and `IsLoaded`. These values can be accessed to determine what state the host is in, for example: ‘IsLoaded’ can be used to determine whether the mod host has a valid mod loaded. 

The following code shows the above example that has been modified to log a message when the host has finished processing a load request. The message changes depending upon the load status of the mod. For more information on the variables that are available take a look at the dedicated scripting reference as well as the example scripts included in the package.

```cs
using System;
using UnityEngine;
using UltimateModdingToolkit;

public class ModLoadExample : MonoBehaviour
{
    private void Start()
    {
        // Create a mod path from a string path
        Uri path = new Uri("file:///C:/Mods/TestMod.mod");
        
        // Issue a mod load request
        ModHost host = Mod.Load(path);
        
        if(host.IsLoaded == true)
        {
            Debug.Log("The mod is loaded");
        }
        else
        {
            Debug.Log("The mod could not be loaded - " + host.LoadResult.Message);
        }
    }
}
```

You are now able to issue mod load requests however you may observe that calling a loading method that can take an undefined amount of time to complete and is not the best practice in a responsive application like a game. It is often good practice to display a loading screen with appropriate progress values that inform the user what is happening and how long they can expect to wait. Take a look at the next section which shows how to implement asynchronous mod loading which takes place on a background thread.