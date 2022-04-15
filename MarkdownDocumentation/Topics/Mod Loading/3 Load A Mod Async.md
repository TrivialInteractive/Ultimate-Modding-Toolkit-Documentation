Async Mod Loading
=================

In order to make uMod easier to use within Unity, we tried to closely mimic the async loading method that Unity uses. As a result, uMod includes a number of custom yieldable objects that are returned by all async load methods which contain information such as the current load progress as well as the end result. As you would expect, all of the objects are yieldable and can be used I coroutines.

The following C# code shows a basic example where a mod is loaded asynchronously. Note that the return value of the load method if now a `ModAsyncOperation`. 

```cs
using System;
using UnityEngine;
using UltimateModdingToolkit;

public class ModCoroutineExample : MonoBehaviour
{   
    private IEnumerator Start()
    {
        // Create a mod path
        Uri path = new Uri("file:///C:/Mods/ExampleMod.mod");
        
        // Create a request
        ModAsyncOperation<ModHost> request = Mod.LoadAsync(path);
        
        // Wait for the task to complete
        yield return request;
        
        // Check the status of the load
        Debug.Log("Loaded = " + request.IsSuccessful);
    }
}
```

> **_NOTE:_** The actual loading will be done on a background thread for the most part so you are able to run other tasks while the request is being processed. Be aware however that there is a small activation portion of the load which must be performed on the main thread so you may see a slight load spike.

That is all quite easy and shifts all the heavy loading away from the main thread however we have no way of knowing how much progress the load has made. This would be very useful to know if we wanted to provide the user with a nice loading bar or progress indicator. Luckily, we can access the current loading progress of an async load request by modifying the above code slightly:

```cs
using System;
using UnityEngine;
using UltimateModdingToolkit;

public class ModCoroutineExample : MonoBehaviour
{   
    private IEnumerator Start()
    {
        // Create a mod path
        Uri path = new Uri("file:///C:/Mods/ExampleMod.mod");
        
        // Create a request
        ModAsyncOperation<ModHost> request = Mod.LoadAsync(path);
                
        while(request.IsDone == false)
        {
            // Display the loading progress
            Debug.Log("Load progress: " + request.Progress);
    
            // This is very important - it allows the main thread to continue and return here in the next frame
            yield return null;
        }
        
        // Check the status of the load
        Debug.Log("Loaded = " + request.IsSuccessful);
    }
}
```

As you can see, the `ModAsyncOperation` object contains a property called `Progress` which returns a normalized value between `0` and `1` representing the current load progress where `0` represent no progress and `1` represents a completed load. We can use this value to update a progress bar or we can even display the load value as a percentage using the `ProgressPercentage` property. 

You can use the `Result` property of a `ModAsyncOperation` to get the load result which in this case would be a mod host.

For more information take a look at the included example scripts which show these async load operations in detail.
