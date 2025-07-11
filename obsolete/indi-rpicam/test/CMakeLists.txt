cmake_minimum_required(VERSION 3.16)
include(CMakeCommon)

FIND_PACKAGE (GMock REQUIRED)
FIND_PACKAGE (Threads REQUIRED)

MESSAGE (STATUS "GTEST_BOTH_LIBRARIES ${GTEST_BOTH_LIBRARIES}")
MESSAGE (STATUS "GTEST_MAIN_LIBRARIES ${GTEST_MAIN_LIBRARIES}")
MESSAGE (STATUS "GTEST_LIBRARIES ${GTEST_LIBRARIES}")
MESSAGE (STATUS "GTEST_INCLUDE_DIRS ${GTEST_INCLUDE_DIRS}")
MESSAGE (STATUS "GMOCK_LIBRARIES ${GMOCK_LIBRARIES}")
MESSAGE (STATUS "GMOCK_INCLUDE_DIRS ${GMOCK_INCLUDE_DIRS}")

ENABLE_TESTING()

INCLUDE_DIRECTORIES ( ${GTEST_INCLUDE_DIRS} )
INCLUDE_DIRECTORIES ( ${GMOCK_INCLUDE_DIRS} )
INCLUDE_DIRECTORIES ( ${CMAKE_SOURCE_DIR} )
INCLUDE_DIRECTORIES ( ${CMAKE_CURRENT_SOURCE_DIR}/.. )

get_filename_component(RPI_DIR ${CMAKE_CURRENT_SOURCE_DIR} DIRECTORY)

SET (test_imx477_SRCS test_imx477.cpp ${RPI_DIR}/indi_rpicam.cpp)
SET (test_imx219_SRCS test_imx219.cpp ${RPI_DIR}/indi_rpicam.cpp)

if (NOT MSVC)
    set (PTHREAD_LIBRARIES -pthread)
endif()

ADD_EXECUTABLE(test_imx477 ${test_imx477_SRCS})
ADD_EXECUTABLE(test_imx219 ${test_imx219_SRCS})

if (NOT MSVC)
    set (PTHREAD_LIBRARIES -pthread)
endif()

SET (test_libs
        rpicam
        ${INDI_DRIVER_LIBRARIES}
        ${CFITSIO_LIBRARIES}
        ${GTEST_BOTH_LIBRARIES}
        ${GMOCK_LIBRARIES}
        ${INDI_LIBRARIES}
        ${MMAL_LIBRARIES}
        ${Threads_LIBRARIES}
        ${PTHREAD_LIBRARIES} 
        ${CMAKE_DL_LIBS})

target_link_libraries(test_imx477 ${test_libs})
target_link_libraries(test_imx219 ${test_libs})

ADD_TEST(test_imx477 test_imx477)
ADD_TEST(test_imx219 test_imx219)
