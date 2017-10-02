# --------------------------------------------------------------------------- #
# Copyright 2017 Ingenieurbüro Krug. All rights reserved.
#
# Author: Florian Eich <florian.eich@ingenieurbuero-krug.de>
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

