cmake_minimum_required(VERSION 3.20)
project(Apolo-Math-Tools)

message(STATUS "Build Googletest")

if (POLICY CMP0135)
    cmake_policy(SET CMP0135 NEW)
endif ()

# GoogleTest requires at least C++14
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(FetchContent)
FetchContent_Declare(
        googletest
        URL https://github.com/google/googletest/archive/03597a01ee50ed33e9dfd640b249b4be3799d395.zip
)

set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(googletest)