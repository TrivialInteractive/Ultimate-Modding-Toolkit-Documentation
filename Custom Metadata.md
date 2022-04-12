Custom Metadata
===============

A built mod package contains standard information such as mod name, version author and more, as well as things like an icon for the mod. This data is standard to all Ultimate Modding Toolkit mods and can be accessed using the `IModInfo` API at runtime or edit time. The main benefit to this metadata is that it can be accessed very quickly by partially loading the mod file header (`Mod.Load`) is not required to access such information). 

In some cases, it would be useful to store additional information in a similar way so that full mod loading is not required in order to access it. This is where custom metadata comes in. This feature allows you to optionally include any amount of custom data in an easy way. 

Define Custom Metadata
----------------------

To create a custom metadata layout, simply create a class that derives from: `UltimateModdingToolkit.ModCustomMetadata`, and define any number of serialized fields as you would normally:

```cs
using UnityEngine;
using UltimateModdingToolkit;

public class MyCustomMetadata : ModCustomMetadata
{
    // This value will be serialized
    public string levelName;
    
    // This value will also be serialized
    [SerializeField]
    private int maxPlayerCount;
}
```

As you can see, the implementation is very simple. Serialization rules will follow Unity standard practice for the most part (public variables will be serialized, attributes like `SerializeField` and `NonSerialized` will be respected) with a few exceptions:
1.	`Unity.Object` or any derived types will not be serialized correctly and will be null at runtime. Custom metadata is only intended to store primitive data types primarily, although arrays and nested types are fully supported.
2.	The amount of serialized data should be kept to a minimum, since custom metadata is intended to be very quick access. Each additional field will add a very slight loading overhead, so large arrays or complex nested types should be discouraged. 


> **_Note:_** _`ModCustomMetadata` is implemented as a `ScriptableObject`. You can receive events like `OnEnable` and `OnDisabled`, although these events may fire before deserializtion has occurred._


The `ModCustomMetadata` type also provides some virtual methods that you can optionally override. These methods are intended as events to notify when Ultimate Modding Toolkit intends to serialize or deserialize the object data:
* `OnBeforeSerialize`: Override this method to receive a callback just before Ultimate Modding Toolkit is about to serialize the metadata object. This will usually be called during the build process.
* `OnAfterDeserialize`: Override this method to receive a callback just after Ultimate Modding Toolkit has deserialized the metadata object. This will usually be called at runtime when a mod is being loaded.

Including Custom Metadata
-------------------------

The next step to setting up custom metadata is to ensure that your metadata type (`MyCustomMetadata` in this instance) is available as part of your game and as part of your mod tools package. Including it as part of your game is simple. Unity will do this for you as long as the script is not inside an `Editor` folder and does not contain editor related code.
Including the script inside your mod tools package is a little more involved simply because you will either need to use assembly definition files, or compile the script into a managed assembly.

**Assembly Definition File**

Your metadata script will need to be setup with an assembly definition file. You can create a new assembly definition in the same folder location as the script if required. There are multiple Unity guides online for working with assembly definition files, so the specific will not be covered in this guide. Once you have setup your assembly definition, you can then add the script to the mod tools package using the Mod Tools Builder Wizard. Go to `Tools -> Ultimate Modding Toolkit -> Mod Tools Builder` to open the wizard and select the `Game API` section.
