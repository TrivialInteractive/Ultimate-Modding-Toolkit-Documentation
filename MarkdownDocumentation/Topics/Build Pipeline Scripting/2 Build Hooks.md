Build Hooks
===========

The Ultimate Modding Toolkit build pipeline is designed to be as flexible for developers as possible. As a result, it is possible to create one or more editor scripts that will be invoked at different stages during the build process. These scripts hook into various stages of the build process depending upon which attribute you use, and can provide a lot of potential for custom validation or automation tools to help your modders.
Before you get into creating build scripts to run custom code using the build API, you will want to be familiar with the build sequence that is followed when creating a mod. This will allow you to select a correct type of build hook for your intended build script. The following diagram shows the sequence that the build engine runs though when building a mod file, and also highlights stages where developers are able to hook into the process to run custom code.
   

Build Engine Sequence
---------------------

The following diagram shows the sequence that the Ultimate Modding Toolkit build engine takes when exporting a mod. Many of these stages are fixed internal stages that are not modifiable, however there are also a number of stages available for you to integrate custom code into the build process. These stages are denoted by an orange colour.

![Image](Images/Build Pipeline Scripting/BuildPipeline.png)