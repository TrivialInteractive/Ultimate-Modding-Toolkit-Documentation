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
