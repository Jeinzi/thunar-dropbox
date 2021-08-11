cmake_minimum_required(VERSION 3.0.0 FATAL_ERROR)
project(thunar-dropbox VERSION 0.3.1 LANGUAGES C)
set(CMAKE_C_FLAGS_RELEASE "-O3")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -Wpedantic")


# Define colors
if(NOT WIN32)
  string(ASCII 27 Esc)
  set(ColorReset "${Esc}[m")
  set(BoldRed    "${Esc}[1;31m")
endif()

# Prevent compilation in-source
if(${CMAKE_BINARY_DIR} STREQUAL ${PROJECT_SOURCE_DIR})
    Message(FATAL_ERROR "${BoldRed}Source and build directories are the same. \
Delete CMakeCache.txt, create an empty build directory, change into it and invoke 'cmake ..'${ColorReset}")
endif()

find_package(PkgConfig REQUIRED)

# gio
pkg_search_module(GIO REQUIRED gio-2.0)
include_directories(${GIO_INCLUDE_DIRS})

# thunarx
pkg_search_module(THUNARX REQUIRED thunarx-3)
include_directories(${THUNARX_INCLUDE_DIRS})

# Source files
file(GLOB SRC "src/*.c")

# Create library and link
add_library(thunar-dropbox SHARED ${SRC})
set_target_properties(thunar-dropbox PROPERTIES PREFIX "")
target_link_libraries(thunar-dropbox ${THUNARX_LIBRARIES})
target_link_libraries(thunar-dropbox ${GIO_LIBRARIES})

# Installation
install(TARGETS thunar-dropbox
        DESTINATION "thunarx-3")
install(FILES "data/icons/hicolor/16x16/apps/thunar-dropbox.png"
        DESTINATION "share/icons/hicolor/16x16/apps")