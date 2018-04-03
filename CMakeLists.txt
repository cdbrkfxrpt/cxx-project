#
# Copyright 2017- Author Write <author.write@esp.tld>
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

#
# require CMake version >= 3.8 to make sure it knows C++17
cmake_minimum_required (VERSION 3.8)
#

#
# set project name and version
project (cxx-project VERSION 1.0.0 LANGUAGES CXX)
#

#
# detect compiler
if (CMAKE_COMPILER_IS_GNUCXX)
  if (CMAKE_CXX_COMPILER_VERSION VERSION_LESS 6.0)
    message (FATAL_ERROR "g++ 6.0 or later required.")
  else ()
    message (WARNING "Compiler needs to be fairly recent for this to compile.")
  endif ()
endif ()
#

#
# set C++ standard and global compiler options
set (CMAKE_CXX_STANDARD 17)
set (CMAKE_CXX_STANDARD_REQUIRED ON)
set (CMAKE_CXX_EXTENSIONS OFF)
#
add_compile_options (-Wall -Wextra -Wpedantic -Werror)
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

