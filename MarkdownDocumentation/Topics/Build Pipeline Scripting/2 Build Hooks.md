Build Hooks
===========

The Ultimate Modding Toolkit build pipeline is designed to be as flexible for developers as possible. As a result, it is possible to create one or more editor scripts that will be invoked at different stages during the build process. These scripts hook into various stages of the build process depending upon which attribute you use, and can provide a lot of potential for custom validation or automation tools to help your modders.
Before you get into creating build scripts to run custom code using the build API, you will want to be familiar with the build sequence that is followed when creating a mod. This will allow you to select a correct type of build hook for your intended build script. The following diagram shows the sequence that the build engine runs though when building a mod file, and also highlights stages where developers are able to hook into the process to run custom code.
   

Build Engine Sequence
---------------------

The following diagram shows the sequence that the Ultimate Modding Toolkit build engine takes when exporting a mod. Many of these stages are fixed internal stages that are not modifiable, however there are also a number of stages available for you to integrate custom code into the build process. These stages are denoted by an orange colour.

![](https://raw.githubusercontent.com/TrivialInteractive/Ultimate-Modding-Toolkit-Documentation/main/MarkdownDocumentation/Images/Build%20Pipeline%20Scripting/BuildPipeline.png)

As you can see from the diagram, there are quite a few stages where you can run custom code. In order to decide where your intended build script should hook into the process, you will first need to decide which type of script you want to implement. There are 3 main types of build script that operate in slightly different ways:

- **Generic Processor:** A generic processor is a build script that derives from `UltimateModdingToolkit.BuildEngine.Build.Processing.BuildProcessor` and defines a single method OnBuildProcess that is called directly by the build engine. The processor will receive a build context object which will provide full access to the current build configuration, and allow you to modify the output as needed.  

    A common use case for a generic processor might be to generate an asset that will be included in the mod without any input from the modder. This asset could be anything from a visual asset such as a Texture or a configuration asset in the form of a scriptable object. You can then use the runtime API’s to access this asset when a mod is loaded in game. You might like to use a similar approach for custom metadata, as outlined in the Custom Metadata section.

- **Asset Processor:** An asset processor is a little bit different because it will derive from `UltimateModdingToolkit.BuildEngine.Build.Processing.BuildAssetProcessor` and it works in a slightly different way. An asset processor has 2 methods that can be overridden which are as follows:
  - **OnBuildProcessAsset:** This method will be called once for each asset of matching type inside the mod export folder. For example: If you create a material processor and the modder includes 2 materials in the mod, this event will be called twice by the build engine. The current processed asset information will be passed into this method so that you can identify the asset in each pass.
  - **OnBuildProcessAssetBatch:** This method will be called once (assuming the mod contains at least one asset of matching type) and will pass in a collection of assets that should be processed. This collection will contain information about all assets in the mod that match the processors filter.

    A common use case for an asset processor might be to add a script component to all prefab assets during the build that your game project will search for. In this case, you would create an asset processor for the `.prefab` asset type. You can then load the asset during the build using the provided API, and then use the standard Unity API to add or remove components as you would normally.

- **Script Compilation Processor:** A script compilation process is a build script that derives from `UltimateModdingToolkit.BuildEngine.Compilation.Processing.ScriptCompilationPreProcessor` or `UltimateModdingToolkit.BuildEngine.Compilation.Processing.ScriptCompilationPostProcessor`, depending upon your requirements. Your build script will need to override the `OnBuildProcessScriptCompilation` method which will be called before or after compilation.
