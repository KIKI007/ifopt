# Copyright (c) 2017, Alexander W. Winkler, ETH Zurich. All rights reserved.
cmake_minimum_required(VERSION 3.1.0 FATAL_ERROR)
project(ifopt VERSION 2.0.6)

if(WIN32)
  set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
endif()

###########
## Build ##
###########
include(GNUInstallDirs) # for correct library locations across platforms
set(config_package_location "share/${PROJECT_NAME}/cmake") # for .cmake find-scripts installs
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)             # so installed solver libraries link to IFOPT/SNOPT                                          
set(CMAKE_INSTALL_RPATH ${CMAKE_INSTALL_PREFIX}/lib)    # so installed solver libraries link to libifopt_core.so  
set(IFOPT_INSTALL_BINDIR ${CMAKE_INSTALL_LIBDIR}/ifopt) # replicate default for catkin installs

IF(NOT CMAKE_BUILD_TYPE MATCHES Release)
  message(STATUS "CMAKE_BUILD_TYPE not set to Release -> impacts performance")
endif()

if(NOT DEFINED BUILD_SHARED_LIBS)
  set(BUILD_SHARED_LIBS ON)
endif()

enable_testing()
set(LIB_CORE ifopt_core)
add_subdirectory(ifopt_core)

option(BUILD_IPOPT "compile with IPOPT solver" ON)
if (BUILD_IPOPT)
  add_subdirectory(ifopt_ipopt)
endif()

option(BUILD_SNOPT "compile with SNOPT solver" OFF)
if (BUILD_SNOPT)
  add_subdirectory(ifopt_snopt)
endif()