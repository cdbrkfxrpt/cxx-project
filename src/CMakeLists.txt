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
  # set name of (part of) build
  set (STAGE_ONE_NAME core)
  #
  # set sources of (part of) build
  set (STAGE_ONE_SOURCES containers.cc components.cc)
  #
  # compile (part of) build to library
  add_library (${STAGE_ONE_NAME} ${STAGE_ONE_SOURCES})
  #
  # set alias to avoid name conflicts with third party code later
  add_library (cxx-project::${STAGE_ONE_NAME} ALIAS ${STAGE_ONE_NAME})
  #
  # set target include directories properly
  target_include_directories (
    ${STAGE_ONE_NAME} INTERFACE ${cxx-project_SOURCE_DIR}/src/include
  )
  #

  #
  # set name of (part of) build
  set (STAGE_TWO_NAME cxx-project)
  #
  # set sources of (part of) build
  set (STAGE_TWO_SOURCES main.cc cxx-project.cc)
  #
  # compile (part of) build to executable
  add_executable (${STAGE_TWO_NAME} ${STAGE_TWO_SOURCES})
  #
  # set target include directories properly
  target_include_directories (${STAGE_TWO_NAME} PUBLIC
    ${cxx-project_SOURCE_DIR}/src/include
    ${cxx-project_SOURCE_DIR}/src/thirdparty
  )
  #
  # set libraries to link target against
  target_link_libraries (${STAGE_TWO_NAME}
    cxx-project::${STAGE_ONE_NAME}
    required_library
    other_lib
  )
  #
else ()
  #
  # you can of course do a different build which works without the library, too
  message (FATAL_ERROR "Can't build without required_library.")
  #
endif ()
#

