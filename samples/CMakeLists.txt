cmake_minimum_required(VERSION 3.0)

project(FindWix)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/../cmake")
find_package(WIX REQUIRED)

add_subdirectory(Minimal)
add_subdirectory(WithExecutable)
add_subdirectory(WithExtension)
add_subdirectory(WithFragment)
add_subdirectory(WithName)