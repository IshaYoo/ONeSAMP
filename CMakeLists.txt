################################################################################
# Project definition

cmake_minimum_required(VERSION 3.13)
project(OneSamp)

# Add main.cpp file of project root directory as source file
set(SOURCE_FILES refactor_main.c)
################################################################################
# Set a default build type if none was specified
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose Release or Debug" FORCE)
endif()
message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")

################################################################################
# Compiler and linker flags

if (CMAKE_BUILD_TYPE STREQUAL "Release")
    set(BUILD_TYPE_FLAGS "-O3")
elseif(CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(BUILD_TYPE_FLAGS "-g")
elseif(CMAKE_BUILD_TYPE STREQUAL "RelWithDebInfo")
    set(BUILD_TYPE_FLAGS "-O3 -g -DNDEBUG")
endif()

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${BUILD_TYPE_FLAGS}")
set(CMAKE_C_FLAGS "${CMAKE_CXX_FLAGS} ${BUILD_TYPE_FLAGS}")

################################################################################
# Source Files and Includes

file(GLOB ARGUMENTS_SOURCES ${CMAKE_SOURCE_DIR}/refactor/arguments/*.c)
file(GLOB MACRO_SOURCES ${CMAKE_SOURCE_DIR}/refactor/macro/*.c)
file(GLOB MEMORY_SOURCES ${CMAKE_SOURCE_DIR}/refactor/memory/*.c)
file(GLOB PARSER_SOURCES ${CMAKE_SOURCE_DIR}/refactor/parser/*.c)
file(GLOB RELEASE_SOURCES ${CMAKE_SOURCE_DIR}/refactor/release/*.c)
file(GLOB STATS_SOURCES ${CMAKE_SOURCE_DIR}/refactor/stats/*.c)
file(GLOB ENGINE_SOURCES ${CMAKE_SOURCE_DIR}/refactor/engine/refactor_engine.c)

set(ONESAMP_LIB_SOURCES
        "${STATS_SOURCES}"
        "${RELEASE_SOURCES}"
        "${PARSER_SOURCES}"
        "${MEMORY_SOURCES}"
        "${MACRO_SOURCES}"
        "${ENGINE_SOURCES}"
        "${ARGUMENTS_SOURCES}")

set(MAIN_SOURCE ${CMAKE_SOURCE_DIR}/refactor/engine/refactor_main.c)

################################################################################
# Executables

# Add executable target with source files listed in SOURCE_FILES variable
add_executable(OneSamp ${MAIN_SOURCE} ${ONESAMP_LIB_SOURCES})
set_target_properties(OneSamp PROPERTIES LINKER_LANGUAGE C)