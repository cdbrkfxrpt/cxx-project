# --------------------------------------------------------------------------- #
# Copyright yyyy Full Name <full.name@emailprovider.tld>
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
# --------------------------------------------------------------------------- #

#
# start out by looking for libraries you might need
find_library (LIB_FOUND required_library)
#

#
# now, you can control your build like so
if (LIB_FOUND)
  #
  # set test target list
  set (TEST_TARGET_LIST
    #
    # put your test targets here
    #
  )
  #

  #
  # set target include directories
  set (INCLUDE_DIRECTORIES
    #
    # put your include directories here, such as:
    ${cxx-project_SOURCE_DIR}/src/include
    ${cxx-project_SOURCE_DIR}/src/thirdparty
    #
  )
  #

  #
  # add and configure executables and add them as tests
  foreach (TARGET ${TEST_TARGET_LIST})
    #
    # create executable for target
    add_executable (${TARGET} main.cc ${TARGET}.cc)
    #

    #
    # specify target include directories
    target_include_directories (${TARGET} PUBLIC ${INCLUDE_DIRECTORIES})
    #

    #
    # if you're building libraries, you will also want to add them here
    target_link_libraries (${TARGET} cxx-project::mylib)
    #

    #
    # add test to CTest
    add_test (NAME ${TARGET} COMMAND ${TARGET})
    #
  endforeach (TARGET)
  #
else ()
  #
  # you can of course do a different build which works without the library, too
  message (FATAL_ERROR "Can't build without required_library.")
  #
endif ()
#

