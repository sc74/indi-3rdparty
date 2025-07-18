PROJECT(libqsi C CXX)
cmake_minimum_required(VERSION 3.16)

LIST(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake_modules/")
LIST(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../cmake_modules/")
include(GNUInstallDirs)
include(CMakeCommon)

SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-error")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-error")

find_package(FTDI1 REQUIRED)
find_package(INDI REQUIRED)

SET(PACKAGE_VERSION "7.6.1")

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/config.h )

ADD_DEFINITIONS(-DUSELIBFTDIONE -DNO_CYUSB -Wall -O2 -fPIC -g)

set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fpermissive")

include_directories( ${CMAKE_CURRENT_BINARY_DIR})
include_directories( ${CMAKE_CURRENT_SOURCE_DIR})
include_directories( ${FTDI1_INCLUDE_DIR})
include_directories( ${INDI_INCLUDE_DIR})
include_directories( ${USB1_INCLUDE_DIR})

set(qsi_LIB_SRCS
    CCDCamera.cpp CameraID.cpp ConvertUTF.c Filter.cpp FilterWheel.cpp HotPixelMap.cpp QSI_PacketWrapper.cpp QSI_USBWrapper.cpp qsiapi.cpp qsicopyright.txt QSIFeatures.cpp
    VidPid.cpp QSIModelInfo.cpp ICameraEeprom.cpp QSI_Interface.cpp QSILog.cpp HostIO_TCP.cpp HostIO_USB.cpp IHostIO.cpp HostConnection.cpp
    QSIError.cpp HostIO_CyUSB.cpp)

#build a shared library
ADD_LIBRARY(qsiapi SHARED ${qsi_LIB_SRCS})

set_target_properties(qsiapi PROPERTIES VERSION 7.6.1 SOVERSION 7)

#need to link to some other libraries ? just add them here
TARGET_LINK_LIBRARIES(qsiapi ${FTDI1_LIBRARIES})

#add an install target here
INSTALL(FILES qsiapi.h QSIError.h DESTINATION include)

INSTALL(TARGETS qsiapi LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})

# build API test
set(qsiapitest_SRCS
   ${qsi_LIB_SRCS}
   ${CMAKE_CURRENT_SOURCE_DIR}/demo_src/qsiapitest.cpp )

add_executable(qsiapitest ${qsiapitest_SRCS})

TARGET_LINK_LIBRARIES(qsiapitest ${FTDI1_LIBRARIES})

install(TARGETS qsiapitest RUNTIME DESTINATION bin )


# build demo
set(qsidemo_SRCS
   ${qsi_LIB_SRCS}
   ${CMAKE_CURRENT_SOURCE_DIR}/demo_src/qsidemo.cpp)

add_executable(qsiapidemo ${qsidemo_SRCS})

TARGET_LINK_LIBRARIES(qsiapidemo ${FTDI1_LIBRARIES})

install(TARGETS qsiapidemo RUNTIME DESTINATION bin )
