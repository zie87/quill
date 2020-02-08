cmake_minimum_required(VERSION 3.1.0)

#-------------------------------------------------------------------------------------------------------
# Use newer policies if possible, up to most recent tested version of CMake.
#-------------------------------------------------------------------------------------------------------
if (${CMAKE_VERSION} VERSION_LESS 3.16)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
else ()
    cmake_policy(VERSION 3.16)
endif ()

#-------------------------------------------------------------------------------------------------------
# Determine if fmt is built as a subproject (using add_subdirectory) or if it is the master project.
#-------------------------------------------------------------------------------------------------------
set(QUILL_MASTER_PROJECT OFF)
if (CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
    set(QUILL_MASTER_PROJECT ON)
endif ()

#-------------------------------------------------------------------------------------------------------
# Custom cmake functions
#-------------------------------------------------------------------------------------------------------
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
include(Utility)

#-------------------------------------------------------------------------------------------------------
# Resolve version
#-------------------------------------------------------------------------------------------------------
quill_extract_version()

project(quill VERSION ${QUILL_VERSION} LANGUAGES CXX)

#-------------------------------------------------------------------------------------------------------
# Set default build to release
#-------------------------------------------------------------------------------------------------------
if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose the type of build" FORCE)
endif ()

#---------------------------------------------------------------------------------------
# Compiler config
#---------------------------------------------------------------------------------------
if (NOT CMAKE_CXX_STANDARD)
    set(CMAKE_CXX_STANDARD 14)
    set(CMAKE_CXX_STANDARD_REQUIRED ON)
endif ()

#-------------------------------------------------------------------------------------------------------
# Options
#-------------------------------------------------------------------------------------------------------

option(QUILL_BUILD_SHARED "Build as shared library" OFF)

option(QUILL_BUILD_EXAMPLES "Build the examples" ${QUILL_MASTER_PROJECT})

option(QUILL_BUILD_TESTS "Build the tests (Requires https://github.com/google/googletest to be installed)" ${QUILL_MASTER_PROJECT})

option(QUILL_BUILD_BENCHMARKS "Build benchmarks" OFF)

option(QUILL_SANITIZE_ADDRESS "Enable address sanitizer in tests" OFF)

option(QUILL_SANITIZE_THREAD "Enable thread sanitizer in tests (Using this option with any other compiler except clang may result to false positives)" OFF)

#-------------------------------------------------------------------------------------------------------
# Required Packages
#-------------------------------------------------------------------------------------------------------
find_package(Threads REQUIRED)

if (QUILL_BUILD_TESTS)
    enable_testing()
    find_package(GTest REQUIRED)
    include_directories(${GTEST_INCLUDE_DIRS})
endif ()

#-------------------------------------------------------------------------------------------------------
# Log Info
#-------------------------------------------------------------------------------------------------------
if (QUILL_MASTER_PROJECT)
    message(STATUS "Build Type: " ${CMAKE_BUILD_TYPE})
    message(STATUS "Quill Version: ${QUILL_VERSION}")
endif ()

#-------------------------------------------------------------------------------------------------------
# Subdirectories
#-------------------------------------------------------------------------------------------------------
add_subdirectory(quill)

# MOVE ME TODO::
# Examples
add_executable(example_01 examples/example_01.cpp)
target_link_libraries(example_01 quill)

add_executable(benchmark_single_caller.cpp examples/benchmark_single_caller.cpp)
target_link_libraries(benchmark_single_caller.cpp quill)