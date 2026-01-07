cmake_minimum_required(VERSION 3.15)
project(ImageLite) 

# 把生成的 “可执行文件” 统一放到 build/bin/ 目录下
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
message(STATUS "可执行文件输出目录: ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}")

add_subdirectory(libs/core)
add_subdirectory(libs/filters)

add_executable(
    imagelite
    apps/cli/src/main.cpp
)

target_link_libraries(
    imagelite
    PRIVATE
        imagelite_core
        imagelite_filters
)

target_include_directories(
    imagelite
    PRIVATE
        libs/core/include
        libs/filters/include
)