################################################################################
# Project definition

cmake_minimum_required(VERSION 3.13)
project(OneSamp)

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
# Check dependencies

find_package(OpenMP)
if(OPENMP_FOUND)
    include_directories(${OpenMP_CXX_INCLUDE_DIRS})
    link_libraries(${OpenMP_CXX_LIBRARIES})
    if(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
        set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS} -fopenmp")
        set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS} -fopenmp")
        set (CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
    elseif(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
        # assumes clang build
        # we can't reliably detect when we're using clang, so for the time being we assume
        set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS} -Xpreprocessor -fopenmp")
        set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS} -Xpreprocessor -fopenmp")
        set (CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
    endif()
endif()

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

add_executable(OneSamp ${MAIN_SOURCE} ${ONESAMP_LIB_SOURCES})
set_target_properties(OneSamp PROPERTIES LINKER_LANGUAGE C)
