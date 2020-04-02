# CMake_Superbuild_Root
CMake Superbuild Example: Root / Superbuild Project

This repository serves as an example of how to work with ExternalProject_Add and CPack.

This root repository will:
- Download and the [Main](https://github.com/amelvill-umich/CMake_Superbuild_Main) and [Extern](https://github.com/amelvill-umich/CMake_Superbuild_Extern) repositories as external projects
- Create a NSIS installer using CPack that contains the binaries built in Main and Extern

Thanks to [Florian](https://discourse.cmake.org/u/florian_chevassu/summary) on the [CMake Discourse](https://discourse.cmake.org/t/how-to-install-an-external-project/888) for the advice on making this
