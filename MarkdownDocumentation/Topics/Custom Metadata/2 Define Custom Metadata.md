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


> **_NOTE:_** _`ModCustomMetadata` is implemented as a `ScriptableObject`. You can receive events like `OnEnable` and `OnDisabled`, although these events may fire before deserializtion has occurred._


The `ModCustomMetadata` type also provides some virtual methods that you can optionally override. These methods are intended as events to notify when Ultimate Modding Toolkit intends to serialize or deserialize the object data:
* `OnBeforeSerialize`: Override this method to receive a callback just before Ultimate Modding Toolkit is about to serialize the metadata object. This will usually be called during the build process.
* `OnAfterDeserialize`: Override this method to receive a callback just after Ultimate Modding Toolkit has deserialized the metadata object. This will usually be called at runtime when a mod is being loaded.

[Continue (Include Custom Metadata)](3 Include Custom Metadata.md)