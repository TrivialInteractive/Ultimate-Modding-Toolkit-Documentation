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

[Continue (Building Custom Metadata)](4 Building CUstom Metadata.md)