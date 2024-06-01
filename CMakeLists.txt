cmake_minimum_required(VERSION 3.11) # FetchContent is available in 3.11+
project(music-visualizer)

# Generate compile_commands.json
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Dependencies
set(RAYLIB_VERSION 5.0)  # Update to the latest version if needed
find_package(raylib ${RAYLIB_VERSION} REQUIRED) # QUIET or REQUIRED

# If raylib is not found, fetch and build it
#[[
if (NOT raylib_FOUND)
  include(FetchContent)
  FetchContent_Declare(
    raylib
    DOWNLOAD_EXTRACT_TIMESTAMP OFF
    URL https://github.com/raysan5/raylib/archive/refs/tags/${RAYLIB_VERSION}.tar.gz
  )
  FetchContent_GetProperties(raylib)
  if (NOT raylib_POPULATED)
    set(FETCHCONTENT_QUIET NO)
    FetchContent_Populate(raylib)
    set(BUILD_EXAMPLES OFF CACHE BOOL "" FORCE) # don't build the supplied examples
    add_subdirectory(${raylib_SOURCE_DIR} ${raylib_BINARY_DIR})
  endif()
endif()
]]

# Add raygui as an interface library
add_library(raygui INTERFACE)
target_include_directories(raygui INTERFACE ${CMAKE_SOURCE_DIR}/src)

# Our Project
add_executable(${PROJECT_NAME} src/main.c)
target_link_libraries(${PROJECT_NAME} raylib raygui m)

# Checks if OSX and links appropriate frameworks (Only required on MacOS)
if (APPLE)
    target_link_libraries(${PROJECT_NAME} "-framework IOKit")
    target_link_libraries(${PROJECT_NAME} "-framework Cocoa")
    target_link_libraries(${PROJECT_NAME} "-framework OpenGL")
endif()

