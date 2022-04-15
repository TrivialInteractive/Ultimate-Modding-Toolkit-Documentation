Build API
=========

The build engine defines a large public API that you can use to interact with the build process at various stages, using one or more build scripts as outlines in the previous section. The API is designed to be easy to use and self-explanatory for the most part, but this section will cover some of the aspects that may be useful for common use cases. It is recommended that you take a look at the included API documentation for aspects that are not covered here.

Build Context
-------------

The build context is the heart of the scriptable build system, and provides access to almost all aspects of the build, from inputs to outputs and in-between. A build context object will be passed into all custom build scripts and you can use it to modify the output mod package.

It also provides access to other useful build services such as logging and progress management which can be used to keep the user updated if your custom script needs to perform complex or lengthy tasks.


Useful API’s
------------

The following section will cover some useful aspects of the build API that build scripts might use in common scenarios. This section does not begin to cover all aspects of the API, and you should refer to the included API documentation for a full overview.
