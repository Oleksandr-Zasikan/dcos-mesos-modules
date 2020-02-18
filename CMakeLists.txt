
if (NOT WIN32)
  cmake_minimum_required(VERSION 3.7.0)
else ()
  cmake_minimum_required(VERSION 3.8.0)
endif ()

project(dcos_mesos_modules)


# CMAKE MODULE SETUP.
#####################
# Paths that are searched when `include(...)` is called.
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/../mesos/cmake)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/../mesos/3rdparty/cmake)
#list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/../mesos/src/cmake)

include(Versions)

get_filename_component(MESOS_ROOT ../mesos ABSOLUTE)



add_subdirectory(journald)