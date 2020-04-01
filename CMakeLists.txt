cmake_minimum_required (VERSION 2.6)

project(SUPERBUILD_ROOT)

include(ExternalProject)

# ----------------------------------------------------------------------------------------------------------------------
# Set up our external projects
# ----------------------------------------------------------------------------------------------------------------------

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

# ----------------------------------------------------------------------------------------------------------------------
# Read the "binary directories" of both external projects
#
# CMake calls the build\extern-prefix\src\extern-build a "Binary directory", but it's really the directory where the VS Solution is, etc.
# I'd call these "build directories", to be honest...
# ----------------------------------------------------------------------------------------------------------------------


ExternalProject_Get_property(extern BINARY_DIR) #this function writes to a variable named BUILD_DIR... okay sure?
set(externBuild ${BINARY_DIR})
message("extern's build directory is " ${externBuild})

unset(BINARY_DIR)

ExternalProject_Get_property(Main BINARY_DIR) #this function writes to a variable named BUILD_DIR... okay sure?
set(mainBuild ${BINARY_DIR})
message("Main's build directory is " ${mainBuild})

# ----------------------------------------------------------------------------------------------------------------------
# Write these paths to CPACK_INSTALL_CMAKE_PROJECTS so that CPack will know to visit these projects for packaging
# ----------------------------------------------------------------------------------------------------------------------

message("CPACK_INSTALL_CMAKE_PROJECTS before append is " ${CPACK_INSTALL_CMAKE_PROJECTS})

# I think these variables have to be set before CPack is included.
set(CPACK_INSTALL_CMAKE_PROJECTS "${CPACK_INSTALL_CMAKE_PROJECTS};${externBuild};extern;ALL;/")
set(CPACK_INSTALL_CMAKE_PROJECTS "${CPACK_INSTALL_CMAKE_PROJECTS};${mainBuild};Main;ALL;/")

message("CPACK_INSTALL_CMAKE_PROJECTS after append is " ${CPACK_INSTALL_CMAKE_PROJECTS})

# ----------------------------------------------------------------------------------------------------------------------
# Now include CPack (must be last)
# ----------------------------------------------------------------------------------------------------------------------

include(CPack)
set(CPACK_GENERATOR NSIS)