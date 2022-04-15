Available Build Hooks
=====================

Once you have decided on the type of build process you would like to create, the next step will be to decide at which stage the build script should run. This decision will mostly depend upon what you want to achieve with the script, so there is no catch all. It is recommended that you study the previous diagram and try to identify a stage that makes most sense to you. If at a later date you realize that perhaps a different stage would be more suitable, it is no problem to change at a later date.
The following section shows how to create a processor for each of the available build hooks. The following implantation examples will be laid out in the order that they are called by the build engine to make the process simpler. Make sure that you select the correct build script type for your intended feature, either generic processor, script processor or asset processor:
