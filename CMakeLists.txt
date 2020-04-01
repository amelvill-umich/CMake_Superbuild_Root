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

#This function writes to a variable named BUILD_DIR... okay sure?
#It'll get clobbered twice so we have to save the result somewhere else.....
ExternalProject_Get_property(extern BINARY_DIR) 
set(externBuildDirectory ${BINARY_DIR})
message("extern's build directory is " ${externBuildDirectory})

unset(BINARY_DIR)

ExternalProject_Get_property(Main BINARY_DIR)
set(mainBuildDirectory ${BINARY_DIR})
message("Main's build directory is " ${mainBuildDirectory})

unset(BINARY_DIR)

# ----------------------------------------------------------------------------------------------------------------------
# Write these paths to CPACK_INSTALL_CMAKE_PROJECTS so that CPack will know to visit these projects for packaging
# ----------------------------------------------------------------------------------------------------------------------

# https://cmake.org/cmake/help/latest/module/CPack.html?highlight=cpack_install_cmake_projects#variable:CPACK_INSTALL_CMAKE_PROJECTS
#                       project build dir       proj name   build all components    "Directory" (??)
set(externProjectInfo   ${externBuildDirectory} "extern"    "ALL"                   "/")
set(mainProjectInfo     ${mainBuildDirectory}   "Main"      "ALL"                   "/")

# both of these go in one list
set(allProjectInfo ${mainProjectInfo} ${externProjectInfo})

message("CPACK_INSTALL_CMAKE_PROJECTS before append is " ${CPACK_INSTALL_CMAKE_PROJECTS})

# I think this variable has to be set before CPack is included.
set(CPACK_INSTALL_CMAKE_PROJECTS ${allProjectInfo})

message("CPACK_INSTALL_CMAKE_PROJECTS after append is " ${CPACK_INSTALL_CMAKE_PROJECTS})

# ----------------------------------------------------------------------------------------------------------------------
# Now include CPack (must be last)
# ----------------------------------------------------------------------------------------------------------------------

include(CPack)
set(CPACK_GENERATOR NSIS)