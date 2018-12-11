cmake_minimum_required(VERSION 3.9)
project(soem VERSION 0.0.1 DESCRIPTION "Ethercat" LANGUAGES C)

include(GNUInstallDirs)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set(SOEM_INCLUDE_INSTALL_DIR include/soem)
set(SOEM_LIB_INSTALL_DIR lib)
set(BUILD_TESTS TRUE)

set(OS "linux")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -Werror")
set(OS_LIBS pthread rt)

file(GLOB SOEM_SOURCES soem/*.c)
file(GLOB OSAL_SOURCES osal/${OS}/*.c)
file(GLOB OSHW_SOURCES oshw/${OS}/*.c)

file(GLOB SOEM_HEADERS soem/*.h)
file(GLOB OSAL_HEADERS osal/osal.h osal/${OS}/*.h)
file(GLOB OSHW_HEADERS oshw/${OS}/*.h)

add_library(soem STATIC
  ${SOEM_SOURCES}
  ${OSAL_SOURCES}
  ${OSHW_SOURCES}
)

target_link_libraries(soem ${OS_LIBS})

# Define headers for this library. PUBLIC headers are used for
# compiling the library, and will be added to consumers' build
# paths.
target_include_directories(soem PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/soem>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/osal>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/osal/${OS}>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/oshw/${OS}>
)

# 'make install' to the correct locations (provided by GNUInstallDirs).
install(TARGETS soem 
    EXPORT libsoemTargets
    LIBRARY  DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE  DESTINATION ${CMAKE_INSTALL_LIBDIR}
) 

install(FILES
  ${SOEM_HEADERS}
  ${OSAL_HEADERS}
  ${OSHW_HEADERS}
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})


include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    libsoemConfigVersion.cmake
    VERSION ${PACKAGE_VERSION}
    COMPATIBILITY AnyNewerVersion
)


install(EXPORT libsoemTargets
        FILE libsoemConfig.cmake
        NAMESPACE libsoem::
        DESTINATION lib/cmake/libsoem
)

