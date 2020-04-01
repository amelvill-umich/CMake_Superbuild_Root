cmake_minimum_required (VERSION 2.6)

project(SUPERBUILD_ROOT)

include(ExternalProject)

# Hacks
# it looks to me that the example from the mailing list expects CMakeLists.txt to be in this directory,
# which means it's the source directory, not the build directory
set(externBuild "C:/_FILES/home/cmbuild/extern-prefix/src/extern-build")
set(mainBuild "C:/_FILES/home/cmbuild/Main-prefix/src/Main-build")


message("CPACK_INSTALL_CMAKE_PROJECTS before append is " ${CPACK_INSTALL_CMAKE_PROJECTS})

# thought there's supposed to be 4 items?
set(CPACK_INSTALL_CMAKE_PROJECTS "${CPACK_INSTALL_CMAKE_PROJECTS};${externBuild};extern;ALL;/")
set(CPACK_INSTALL_CMAKE_PROJECTS "${CPACK_INSTALL_CMAKE_PROJECTS};${mainBuild};Main;ALL;/")

message("CPACK_INSTALL_CMAKE_PROJECTS after append is " ${CPACK_INSTALL_CMAKE_PROJECTS})

include(CPack)
set(CPACK_GENERATOR NSIS)


ExternalProject_Add(extern
                    GIT_REPOSITORY "https://github.com/amelvill-umich/CMake_Superbuild_Extern"
                    CMAKE_ARGS
                        -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_CURRENT_BINARY_DIR}/extern-install
)

ExternalProject_Add(Main
                    GIT_REPOSITORY "https://github.com/amelvill-umich/CMake_Superbuild_Main"
                    CMAKE_ARGS
                        -DSUPERBUILD_EXTERN_DIR:PATH=${CMAKE_CURRENT_BINARY_DIR}/extern-install/lib/cmake/Extern
                        -DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_CURRENT_BINARY_DIR}/Main-install
                    DEPENDS 
                        extern
)

