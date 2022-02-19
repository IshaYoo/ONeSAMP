cmake_minimum_required(VERSION 3.13)
project(OneSamp)

# Add main.cpp file of project root directory as source file
set(SOURCE_FILES refactor_main.c)

# Add executable target with source files listed in SOURCE_FILES variable
add_executable(OneSamp ${SOURCE_FILES})