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

if(CMAKE_SYSTEM_PROCESSOR MATCHES "^arm.*")
  option(BUILD_HAL_ARM335XQEP_MANAGED_DRIVER_MODULE
         "Built the Arm335xQEP HAL managed driver module." TRUE)

  if(BUILD_HAL_ARM335XQEP_MANAGED_DRIVER_MODULE)
    include(MachinekitHALSymbolVisibilityFunction)

    add_library(hal_arm335xqep MODULE)

    set(SOURCE_FILES src/hal_arm335xQEP.c)

    set(PRIVATE_HEADER_FILES src/hal_arm335xQEP.h)

    target_sources(hal_arm335xqep PRIVATE ${SOURCE_FILES}
                                          ${PRIVATE_HEADER_FILES})

    target_link_libraries(hal_arm335xqep PRIVATE managed_hal managed_runtime
                                                 user_pci)

    export_rtapi_symbols(TARGET hal_arm335xqep)

    set_target_properties(
      hal_arm335xqep
      PROPERTIES PREFIX "mod"
                 LIBRARY_OUTPUT_DIRECTORY
                 "${MACHINEKIT_HAL_MANAGED_MODULE_OUTPUT_DIRECTORY}")

    install(
      TARGETS hal_arm335xqep
      EXPORT machinekit_hal_managed_driver_module_hal_arm335xqep
      LIBRARY DESTINATION "${MACHINEKIT_HAL_MANAGED_MODULE_DIRECTORY}"
              COMPONENT MachinekitHAL_Managed_Module_HAL_ARM335xQEP_Drivers)

    cpack_add_component(
      MachinekitHAL_Managed_Module_HAL_ARM335xQEP_Drivers
      GROUP MachinekitHAL_Managed_Module_HAL_ARM335xQEP
      DEPENDS MachinekitHAL_Library_User_PCI_Libraries)

    # Specification of artifacts placement in package tree
    cpack_add_component_group(
      MachinekitHAL_Managed_Module_HAL_ARM335xQEP
      PARENT_GROUP MachinekitHAL_Package_Base_Managed_Modules_Drivers)
  endif()
endif()
