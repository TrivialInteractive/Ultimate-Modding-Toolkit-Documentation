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


> **_NOTE:_** _`ModCustomMetadata` is implemented as a `ScriptableObject`. You can receive events like `OnEnable` and `OnDisabled`, although these events may fire before deserializtion has occurred._


The `ModCustomMetadata` type also provides some virtual methods that you can optionally override. These methods are intended as events to notify when Ultimate Modding Toolkit intends to serialize or deserialize the object data:
* `OnBeforeSerialize`: Override this method to receive a callback just before Ultimate Modding Toolkit is about to serialize the metadata object. This will usually be called during the build process.
* `OnAfterDeserialize`: Override this method to receive a callback just after Ultimate Modding Toolkit has deserialized the metadata object. This will usually be called at runtime when a mod is being loaded.

Including Custom Metadata
-------------------------

The next step to setting up custom metadata is to ensure that your metadata type (`MyCustomMetadata` in this instance) is available as part of your game and as part of your mod tools package. Including it as part of your game is simple. Unity will do this for you as long as the script is not inside an `Editor` folder and does not contain editor related code.
Including the script inside your mod tools package is a little more involved simply because you will either need to use assembly definition files, or compile the script into a managed assembly.

**Assembly Definition File**  
Your metadata script will need to be setup with an assembly definition file. You can create a new assembly definition in the same folder location as the script if required. There are multiple Unity guides online for working with assembly definition files, so the specific will not be covered in this guide. Once you have setup your assembly definition, you can then add the script to the mod tools package using the Mod Tools Builder Wizard. Go to `Tools -> Ultimate Modding Toolkit -> Mod Tools Builder` to open the wizard and select the `Game API` section.


> **_NOTE_:** _You may need to input required values into the Mod Tools Builder before you can advance to the `Game API` section. See the [Mod Tools Builder]() section for details._

Under the `Assembly Definitions` foldout, use the folder tree view to navigate to the corresponding assembly definition file (.asmdef). Tick the checkbox next to the appropriate assembly definition file and the script now be included as part of the mod tools package. You can continue to export the package now or at a later date (The `Game API` configuration will be remembered) and it will be setup for use with your custom metadata type.

**Managed Assembly**  
This approach is a little more advanced as it requires the use of an external IDE/Compiler to compile the source code into a managed assembly. First, remove the `MyCustomMetadata` script from the project as it will be replaced with the equivalent assembly. Using your favourite IDE/Compiler, create a new C# project with the output set as `.dll` and compile the script that you removed from the project `MyCustomMetadata`. There are many guides on compiling managed assemblies for use with Unity online, and it will not be covered in this user guide.
Once you have created your assembly, drag and drop/import it into your Unity project at a suitable location, For example: `Assets/ModInterface/`. Unity will recompile scripts and load the assembly without error. You can now use the Mod Tools Builder Wizard to create your mod tools package. Go to `Tools -> Ultimate Modding Toolkit -> Mod Tools Builder` to open the wizard and select the `Game API` section.

> **_NOTE_:** _You may need to input required values into the Mod Tools Builder before you can advance to the `Game API` section. See the Mod Tools Builder section of this user guide for details._

Under the `Assemblies` foldout, use the folder tree view to navigate to the assembly that you imported and tick the checkbox next to its name. You can continue to export the package now or at a later date (The `Game API` configuration will be remembered) and it will be setup for use with your custom metadata type.

Building Custom Metadata
------------------------

One the custom metadata script is included in your mod tools package, the next step is to attach that metadata so that it is built into a mod during export. There are a couple of ways you can do this, but it would be best to understand how custom metadata is used by the build engine first.
As mentioned previously, custom metadata is implemented as a scriptable object, meaning that you can create an asset from it using the Unity API. The build engine will simply look for any scriptable objects deriving from `ModCustomMetadata` inside the mod export folder during a build. If an asset is found, it will be serialized and built into the mod automatically. 


> **_NOTE_:** _Only one custom metadata asset should exist inside the mod export folder during a build. The build engine will report an error if multiple assets are found and the first detected asset will be serialized into the mod._


That leaves you with a couple of options of how to get the custom metadata asset into the mod export folder:

1. **Instruct the modder to create this metadata asset**  

A simple but possibly effective way to add this metadata asset into a build would be to simply provide instructions to the modder to do so. You can add the ‘CreateAssetMenu’ attribute to your metadata class declaration so that modders are able to create assets easily. You can then provide documentation as part of a modding guide showing how to create and fill out this metadata asset.

If the modder fails to create this metadata asset prior to export, then the build will still complete successfully (assuming no other errors), but the custom metadata will be null at runtime. You could report an error at this stage if the metadata is mandatory to indicate that the mod does not contain the expected data.

We could however go one step further and prevent a build from completing if the modder does not add our custom metadata asset into the build. This can be done by creating a simple build script to check for the expected metadata. Here is a quick example of such a validator:

```cs
using UltimateModdingToolkit.BuildPipeline;
using UltimateModdingToolkit.BuildPipeline.Build;
using UltimateModdingToolkit.BuildEngine.Processing;

[BuildAssetProcessor(".asset", 1000)]
internal sealed class CustomMetadataValidator : BuildAssetProcessor
{
    public override void OnBuildProcessAsset(BuildContext context, BuildPipelineAsset asset, BuildProgressTask progress)
    {
        if(context.BuildCustomMetadata == null || 
            context.BuildCustomMetadata.GetType() != typeof(MyCustomMetadata))
        {
            context.FailBuild("The mod does not include the required custom metadata. Please see modding documentaton!);
        }
    }
}
```

This simple build script will cause the build to fail if the mod export folder does not include a custom metadata asset of the expected type.


 > **_NOTE_:** _This script is should be placed inside an editor folder and is intended to run during a mod build. You will need to include it in your mod tools package under the content section so that it is available in mod project._


2. **Generate the metadata asset automatically**  

Another option that you could choose is to automatically generate the metadata asset any time a new mod is created. This way the metadata asset will already exist in the mod export folder and the modder can simply select the asset to fill out the fields. This approach will require a little bit of editor scripting to perform the asset creation though.

The first thing you will want to do is create an editor script which executes a method any time the modder selects to create a new mod. If you are using the provided `UltimateModdingToolkit.Exporter` editor tools and not a custom approach, this can be achieved by adding an events listener to the `OnCreateMod` event:

```cs
using UnityEditor;
using UltimateModdingToolkit.Exporter;

[InitializeOnLoad]
internal static class CustomMetadataCreator
{
    static CustomMetadataCreator()
    {
        ExporterUtil.OnCreateMod += OnUserCreatedMod;
    }
    
    private static void OnUserCreatedMod(CreateModEventArgs e)
    {
        Debug.Log("The user created a new mod at: " + e.relativePath);
    }
}
```

The `OnUserCreateMod` method will be called any time the modder selected `Tools -> <mod tools name> -> Create New Mod`. At this point, we can simply use the Unity API to create an asset from our custom metadata type inside the mod export folder:

```cs
private static void OnUserCreatedMod(CreateModEventArgs e)
{
    MyCustomMetadata metadataInstance = ScriptableObject.CreateInstance<MyCustomMetadata>();
    
    AssetDatabase.CreateAsset(e.relativePath, "MyMetadata.asset");
    AssetDatabase.SaveAssets();
}
```


> **_NOTE_:** _This script should be placed inside an editor folder and is intended to run during a mod build. You will need to include it in your mod tools package under the content section so that it is available in mod project._


We can use this editor script to create the metadata asset for the user automatically. We can also use the validator editor script from the previous section in conjunction to ensure that the modder does not remove or delete the asset at any point


> **_NOTE_:** _It would be wise to provide the modder with documentation on how to fill out the metadata fields, and what suitable values might look like._  


3. **Generate the metadata asset automatically at build time**  

For advanced developers, it may be desirable to simplify the process even more for your modders by automating the process further. In some cases, you may be able to generate the actual metadata values automatically and take the modder out of the equation. In this case, it might be better suited to generate the asset completely at build time so that the modder does not need to even see the metadata asset.

This can be achieved by implementing a custom build collector editor script to add additional assets to the build:

```cs
using UltimateModdingToolkit.BuildPipeline.Build;
using UltimateModdingToolkit.BuildEngine;
using UltimateModdingToolkit.BuildEngine.Build.Processing;

[BuildCollectAssetsProcessor]
internal sealed class MetadataCollector : BuildProcessor
{
    public override void OnBuildProcess(BuildEngineService buildService, BuildContext context)
    {
        // Generate the asset as normal
        MyCustomMetadata metadataInstance = ScriptableObject.CreateInstance<MyCustomMetadata>();
    
        AssetDatabase.CreateAsset(e.relativePath, "MyMetadata.asset");
        AssetDatabase.SaveAssets();
        
        // Add the asset to the build
        BuildPipelineAsset asset = context.BuildAssets.IncludeAsset(e.relativePath + "/MyMetadata.asset");
        
        // Inform the build engine that the asset should be deleted during the cleanup stage
        asset.MarkAsModified();
        asset.MarkAsGenerated();
    }
}
```


> **_NOTE_:** _This script should be placed inside an editor folder and is intended to run during a mod build. You will need to include it in your mod tools package under the content section so that it is available in mod project._


You can also use a similar approach if you do not want the modder to interact with the metadata asset directly. Instead, you can provide a custom editor window or drawer to display an easy-to-follow interface for your modder to fill out any required metadata. Using an adaptation of the above code, you can then generate the metadata asset during the build based on values specified in your custom interface. 

Describing how to create such an interface for interaction with the modder is outside the scope of this topic. This guide assumes that the developer will have suitable knowledge of the Unity editor and related API’S if they intend to create such an interface. In that case, it should be enough to create a working solution based on the code samples above and the Ultimate Modding Toolkit API documentation.
