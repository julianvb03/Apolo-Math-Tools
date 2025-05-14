cmake_minimum_required(VERSION 3.20)

# === Project Metadata ===
project(Apolo-Math-Tools LANGUAGES CXX)

# === C++ Standard Configuration ===
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED YES)

# === Platform Check ===
if (NOT ${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
    message(STATUS "This system is ${CMAKE_SYSTEM_NAME}")
    message(FATAL_ERROR "This project is only supported on Linux.")
endif()

# === Compiler Detection ===
if (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    set(ACTUAL_COMPILER "clang")
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    set(ACTUAL_COMPILER "gnu")
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "Intel")
    set(ACTUAL_COMPILER "intel")
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "NVIDIA")
    set(ACTUAL_COMPILER "nvidia")
else()
    set(ACTUAL_COMPILER "unknown")
    message(FATAL_ERROR "Unsupported compiler: ${CMAKE_CXX_COMPILER_ID}")
endif()

message(STATUS "Compiler: ${CMAKE_CXX_COMPILER_ID}")

# === Compiler Flags ===
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -std=c++23")

# === Optional Debug Configuration ===
option(DEBUG_MODE "Enable debug mode" ON)
if (DEBUG_MODE)
    message(STATUS "Debug mode is ON")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -O0 -fprofile-arcs -ftest-coverage \
        -Wpedantic -Wconversion -Wsign-conversion                       \
        -Wshadow -Wnull-dereference -Wdouble-promotion -Wformat=2       \
        -Wunused -Wduplicated-cond -Wduplicated-branches                \
        -Wlogical-op -Wuseless-cast -Wno-unused-parameter               \
        -Wnon-virtual-dtor -Wold-style-cast -Woverloaded-virtual        \
        -Wmissing-declarations -Wmissing-include-dirs -Wswitch-enum")
endif()

# === Custom Modules ===
list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake")
include(ColorizedMessages)

# === Output Directories ===
include(GNUInstallDirs)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${CMAKE_INSTALL_BINDIR}" CACHE PATH "Directory for the built binaries")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${CMAKE_INSTALL_LIBDIR}" CACHE PATH "Directory for the built libraries")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${CMAKE_INSTALL_LIBDIR}" CACHE PATH "Directory for the built static libraries")

# === Modules Configuration ===
set(GAUSS_SEIDEL_IMP "seq" CACHE STRING "Available implementations: seq, omp, mpi, cuda")

if (NOT GAUSS_SEIDEL_IMP IN_LIST ALLOWED_GAUSS_SEIDEL_IMPLS)
    set(ALLOWED_GAUSS_SEIDEL_IMPLS seq omp mpi cuda)
    message(FATAL_ERROR "Invalid GAUSS_SEIDEL_IMP=${GAUSS_SEIDEL_IMP}. Must be one of: ${ALLOWED_GAUSS_SEIDEL_IMPLS}")
endif()
