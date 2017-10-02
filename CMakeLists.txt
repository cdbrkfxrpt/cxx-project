# --------------------------------------------------------------------------- #
# Copyright 2017 Ingenieurbüro Krug. All rights reserved.
#
# Author: Florian Eich <florian.eich@ingenieurbuero-krug.de>
# --------------------------------------------------------------------------- #

#
# require CMake version >= 3.8 to make sure it knows C++17
cmake_minimum_required (VERSION 3.8)
#

#
# set project name and version
project (cxx-project CXX)
#
set (cxx-project_VERSION_MAJOR 1)
set (cxx-project_VERSION_MINOR 0)
set (cxx-project_VERSION_PATCH 0)
#

#
# detect compiler
if (CMAKE_COMPILER_IS_GNUCXX)
  if (CMAKE_CXX_COMPILER_VERSION VERSION_LESS 6.0)
    message (FATAL_ERROR "g++ 6.0 or later required.")
  endif ()
endif ()
#

#
# set C++ standard and global compiler options
set (CMAKE_CXX_STANDARD 17)
set (CMAKE_CXX_STANDARD_REQUIRED ON)
set (CMAKE_CXX_EXTENSIONS OFF)
#
add_compile_options (-Wall -Wextra -Wpedantic -Werror -Wfatal-errors)
#

#
# compile commands for youcompleteme (vim plugin)
set (CMAKE_EXPORT_COMPILE_COMMANDS ON)
#

#
# set output directory
set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
#

#
# add the code
add_subdirectory (${cxx-project_SOURCE_DIR}/src)
#

#
# enable testing and add unit tests
enable_testing ()
add_subdirectory (${cxx-project_SOURCE_DIR}/tests)
#

