# ~~~
# ####################################################################
# Description:  CMakeLists.txt
#
#               This file, 'CMakeLists.txt', implements build system
#               rules for Machinekit-HAL project
#
# Copyright (C) 2021    Jakub Fišer  <jakub DOT fiser AT eryaf DOT com>
#
# This program is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public
# License as published by the Free Software Foundation; either
# version 2.1 of the License, or (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# Lesser General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public
# License along with this library; if not, write to the Free Software
# Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301  USA
#
# #####################################################################
# ~~~

cmake_minimum_required(VERSION 3.22)

set(CMAKE_EXPORT_COMPILE_COMMANDS TRUE)
set(CMAKE_EXPORT_LINK_COMMANDS TRUE)
set(CMAKE_DISABLE_SOURCE_CHANGES TRUE)
set(CMAKE_DISABLE_IN_SOURCE_BUILD TRUE)

file(READ "${CMAKE_CURRENT_SOURCE_DIR}/VERSION" MachinekitHALVersion)
string(STRIP "${MachinekitHALVersion}" MachinekitHALVersion)

project(
  Machinekit-HAL
  VERSION "${MachinekitHALVersion}"
  DESCRIPTION "Universal framework for machine control."
  HOMEPAGE_URL "https://machinekit.io"
  LANGUAGES C CXX)

if("${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_CURRENT_SOURCE_DIR}")
  include(CTest)
  enable_testing()
endif()

if(CMAKE_TOOLCHAIN_FILE)
  cmake_path(IS_RELATIVE CMAKE_TOOLCHAIN_FILE _toolchain_file_is_relative)
  if(_toolchain_file_is_relative)
    file(REAL_PATH "${CMAKE_TOOLCHAIN_FILE}" _toolchain_path BASE_DIRECTORY
         "${CMAKE_BINARY_DIR}" EXPAND_TILDE)
    if(NOT EXISTS "${_toolchain_path}")
      message(
        FATAL_ERROR "Specified Toolchain file ${_toolchain_path} doesn't exist!"
      )
    endif()
    set(CMAKE_TOOLCHAIN_FILE "${_toolchain_path}")
    message(
      WARNING
        "CMAKE_TOOLCHAIN_FILE set to ${CMAKE_TOOLCHAIN_FILE} to resolve the relative path issue. Please, avoid using relative paths!"
    )
    unset(_toolchain_path)
  endif()
  unset(_toolchain_file_is_relative)
endif()

string(REGEX REPLACE "[-~ .]" "_" MACHINEKIT_HAL_PACKAGE_PREFIX_NORMALIZED
                     ${CMAKE_PROJECT_NAME})
string(TOLOWER ${MACHINEKIT_HAL_PACKAGE_PREFIX_NORMALIZED}
               MACHINEKIT_HAL_PACKAGE_PREFIX)
string(REGEX REPLACE "_" "/" MACHINEKIT_HAL_PACKAGE_PREFIX_PATH
                     ${MACHINEKIT_HAL_PACKAGE_PREFIX})
string(REGEX REPLACE "[-~ .]" "" MACHINEKIT_HAL_PACKAGE_NAME
                     ${CMAKE_PROJECT_NAME})

include(GNUInstallDirs)
include(CPackComponent)
include(CPack)

set(MACHINEKIT_HAL_NAMESPACE "Machinekit::HAL")
set(MACHINEKIT_HAL_EXECUTABLE_DIRECTORY "${CMAKE_INSTALL_BINDIR}")
set(MACHINEKIT_HAL_LIBRARY_DIRECTORY "${CMAKE_INSTALL_LIBDIR}")
set(MACHINEKIT_HAL_MANAGED_MODULE_DIRECTORY
    "${CMAKE_INSTALL_LIBDIR}/${MACHINEKIT_HAL_PACKAGE_PREFIX_PATH}/module/managed"
)
set(MACHINEKIT_HAL_UNMANAGED_MODULE_DIRECTORY
    "${MACHINEKIT_HAL_EXECUTABLE_DIRECTORY}")
set(MACHINEKIT_HAL_INTERNAL_EXECUTABLE_DIRECTORY
    "${CMAKE_INSTALL_LIBEXECDIR}/${MACHINEKIT_HAL_PACKAGE_PREFIX_PATH}")
set(MACHINEKIT_HAL_INTERFACE_DIRECTORY
    "${CMAKE_INSTALL_INCLUDEDIR}/${MACHINEKIT_HAL_PACKAGE_PREFIX_PATH}")
set(MACHINEKIT_HAL_SYSTEM_CONFIG_DIRECTORY
    "${CMAKE_INSTALL_SYSCONFDIR}/${MACHINEKIT_HAL_PACKAGE_PREFIX_PATH}")
set(MACHINEKIT_HAL_LOCAL_STATE_DIRECTORY "${CMAKE_INSTALL_LOCALSTATEDIR}")
set(MACHINEKIT_HAL_TEST_DIRECTORY
    "${CMAKE_INSTALL_DATAROOTDIR}/${MACHINEKIT_HAL_PACKAGE_PREFIX_PATH}/testsuite"
)
set(MACHINEKIT_HAL_CMAKE_PACKAGE_DIRECTORY
    "${CMAKE_INSTALL_LIBDIR}/cmake/${CMAKE_PROJECT_NAME}")

list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/support/cmake/tools"
     "${PROJECT_SOURCE_DIR}/support/cmake/languages")

set(CMAKE_C_STANDARD 11)
set(CMAKE_CC_STANDARD 17)
set(CMAKE_CXX_STANDARD 14)

# TODO: Make a real effort at setting relevant flags
#set(CMAKE_C_FLAGS_DEBUG "-g -O0")
#set(CMAKE_CXX_FLAGS_DEBUG "-g -O0")
#set(CMAKE_C_FLAGS_RELEASE "-O0")
#set(CMAKE_CXX_FLAGS_RELEASE "-O0")
#set(CMAKE_C_FLAGS_RELWITHDEBINFO "-g -O0")
#set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "-g -O0")

message(STATUS "Value of CMAKE_CXX_FLAGS: ${CMAKE_CXX_FLAGS}")
message(STATUS "Value of CMAKE_C_FLAGS: ${CMAKE_C_FLAGS}")
message(STATUS "Value of CMAKE_EXE_LINKER_FLAGS: ${CMAKE_EXE_LINKER_FLAGS}")

define_property(
  TARGET
  PROPERTY "PUBLIC_INCLUDE_DIRECTORY"
  BRIEF_DOCS "Header directory for a target"
  FULL_DOCS
    [[
    Special traget specific property for storing a path to directory where PUBLIC HEADERS
    live, typically the "include/package_name" one
  ]])

add_subdirectory(src)

# Main package CPack groups
cpack_add_component_group(MachinekitHAL_Package_Base_Executables)
cpack_add_component_group(MachinekitHAL_Package_Base_Libraries)
cpack_add_component_group(MachinekitHAL_Package_Base_Libraries_Development)
cpack_add_component_group(MachinekitHAL_Package_Base_Managed_Modules_Components)
cpack_add_component_group(MachinekitHAL_Package_Base_Managed_Modules_Drivers)
cpack_add_component_group(MachinekitHAL_Package_Base_Managed_Modules_Drivers_Development)
cpack_add_component_group(MachinekitHAL_Package_Base_Unmanaged_Modules_Components)
cpack_add_component_group(MachinekitHAL_Package_Base_Unmanaged_Modules_Drivers)
cpack_add_component_group(MachinekitHAL_Package_Base_Tests_Runtests)
