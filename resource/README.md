# OpenCensus Library Resource Package
This documentation serves to document the "look and feel" of the OpenCensus resource package.
It describes their key types and overall behavior.

The primary purpose of resources as a first-class concept in the core library is decoupling
of discovery of resource information from exporters. This allows for independent development
of easy customization for users that need to integrate with closed source environments.

## Main APIs
* [Resource](Resource.md)
