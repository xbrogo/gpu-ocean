PROJECT(mgr)
CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
	
IF(NOT LIBRARY_OUTPUT_PATH)
	SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
ENDIF(NOT LIBRARY_OUTPUT_PATH)

FIND_PACKAGE(NetCDF QUIET)

OPTION(mgr_USE_NETCDF "Enable or disable NetCDF-support" ${NetCDF_FOUND})
OPTION(mgr_USE_NETCDF_COMPRESSION "Enable or disable NetCDF compression-support (Requires new version of netcdf)" FALSE)

configure_file(${mgr_SOURCE_DIR}/src/config.h.in ${mgr_SOURCE_DIR}/src/config.h @ONLY)

INCLUDE_DIRECTORIES(
	${mgr_SOURCE_DIR}/src
    ${sim_INCLUDE_DIR}
	${Boost_INCLUDE_DIR}
	${CMAKE_CURRENT_BINARY_DIR}
	${CMAKE_BINARY_DIR}/sim)

LINK_DIRECTORIES(${Boost_LIBRARY_DIRS})

IF(CMAKE_SIZEOF_VOID_P EQUAL 8)
	SET(mgr_TARGET_CPU_ARCH "64")
ELSEIF(CMAKE_SIZEOF_VOID_P EQUAL 4)
	SET(mgr_TARGET_CPU_ARCH "32")
ELSE()
	MESSAGE(ERROR "Could not deduce target architecture: sizeof(void*) is neither 4 or 8...")
ENDIF()

IF(mgr_USE_NETCDF)
    ADD_LIBRARY(mgr STATIC src/manager.cpp src/programoptions.cpp src/initconditions.cpp src/field.cpp src/netcdfwriter.cpp src/netcdfreader.cpp)
ELSE()
	ADD_LIBRARY(mgr STATIC src/manager.cpp src/programoptions.cpp src/initconditions.cpp src/field.cpp)
ENDIF()
SET_TARGET_PROPERTIES(mgr PROPERTIES DEBUG_POSTFIX "d")
INSTALL(TARGETS mgr DESTINATION mgr/lib)
INSTALL(DIRECTORY include DESTINATION mgr PATTERN ".svn" EXCLUDE)
