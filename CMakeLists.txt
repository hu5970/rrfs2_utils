cmake_minimum_required(VERSION 3.15)

project(RRFS_Utl
	VERSION 1.0.0
	LANGUAGES C Fortran)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
set(CMAKE_DIRECTORY_LABELS ${PROJECT_NAME})

include(compiler_flags)
include(GNUInstallDirs)

if(NOT CMAKE_BUILD_TYPE MATCHES "^(Debug|Release|RelWithDebInfo|MinSizeRel|Production)$")
  message(STATUS "Setting build type to 'Release' as none was specified.")
  set(CMAKE_BUILD_TYPE
      "Release"
      CACHE STRING "Choose the type of build." FORCE)
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "MinSizeRel" "RelWithDebInfo" "Production")
endif()

if(NOT CMAKE_C_COMPILER_ID MATCHES "^(GNU|Intel|Clang|AppleClang)$")
  message(WARNING "${CMAKE_C_COMPILER_ID} is not supported.")
endif()

if(NOT CMAKE_Fortran_COMPILER_ID MATCHES "^(GNU|Intel)$")
  message(WARNING "${CMAKE_Fortran_COMPILER_ID} is not supported.")
endif()

# User options
option(OPENMP "Enable OpenMP Threading" ON)

# Echo user options
message(STATUS "OPENMP ................. ${OPENMP}")

list(APPEND SUBLIST "USE_RAPHRRR_SFC")
option(RRFS_UTL_ALL "" ON)
foreach(X IN LISTS SUBLIST)
  string(TOLOWER ${X} x)
  option(${X} "Enable building ${x}.fd" ${RRFS_UTL_ALL})
endforeach()

# Dependencies
find_package(MPI REQUIRED)
find_package(NetCDF REQUIRED Fortran)
if(OPENMP)
  find_package(OpenMP REQUIRED)
endif()

# NCEPLibs dependencies
#find_package(bufr REQUIRED)

# Other dependencies

add_subdirectory(baselib)

foreach(X IN LISTS SUBLIST)
  if(${${X}})
    string(TOLOWER ${X} x)
    add_subdirectory(${x}.fd)
  endif()
endforeach()
