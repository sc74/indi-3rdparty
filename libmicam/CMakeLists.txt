cmake_minimum_required(VERSION 3.16)
project (libmicam)

set (MICAM_VERSION "0.12.0")
set (MICAM_VERSION_MAC "0.11.0")
set (MICAM_SOVERSION "0")

option(INDI_INSTALL_UDEV_RULES "Install UDEV rules" On)

list (APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake_modules/")
list (APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../cmake_modules/")
include (GNUInstallDirs)
include(CMakeCommon)
include (InstallImported)

add_library (gxccd SHARED IMPORTED)

set_target_properties (gxccd PROPERTIES VERSION ${MICAM_VERSION} SOVERSION ${MICAM_SOVERSION})

if (APPLE)

  set_target_properties (gxccd PROPERTIES VERSION ${MICAM_VERSION_MAC})

  set_property (TARGET gxccd PROPERTY IMPORTED_LOCATION "libgxccdmac.bin")

  FIX_MACOS_LIBRARIES("libgxccd" "libgxccdmac.bin" "Moravian/GXCCD")

elseif (UNIX AND NOT WIN32)

  if (CMAKE_SYSTEM_PROCESSOR MATCHES "armv+")
    set_property (TARGET gxccd PROPERTY IMPORTED_LOCATION "libgxccdarmv7.bin")
  elseif (CMAKE_SYSTEM_PROCESSOR MATCHES "aarch64")
    set_property (TARGET gxccd PROPERTY IMPORTED_LOCATION "libgxccdarmv8.bin")
  elseif (CMAKE_SYSTEM_PROCESSOR MATCHES "x86_64")
    set_property (TARGET gxccd PROPERTY IMPORTED_LOCATION "libgxccd64.bin")
  elseif (CMAKE_SYSTEM_PROCESSOR MATCHES "i[3-6]86")
    set_property (TARGET gxccd PROPERTY IMPORTED_LOCATION "libgxccd32.bin")
  endif ()

  # Install udev rules
  if (INDI_INSTALL_UDEV_RULES)
    set (UDEVRULES_INSTALL_DIR "/lib/udev/rules.d" CACHE STRING "Base directory for udev rules")
    install (FILES 99-miccd.rules DESTINATION ${UDEVRULES_INSTALL_DIR})
  endif ()

endif ()

# Install header files
install (FILES gxccd.h DESTINATION include/libmicam)

# Install library
install_imported (TARGETS gxccd DESTINATION ${CMAKE_INSTALL_LIBDIR})
