cmake_minimum_required(VERSION 3.15)
project(ImageLite) 

# 把生成的 “可执行文件” 统一放到 build/bin/ 目录下
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
message(STATUS "可执行文件输出目录: ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}")

set(SAMPLE_IMAGES_DIR ${PROJECT_SOURCE_DIR}/sample_images)
message(STATUS "样例图片目录: ${SAMPLE_IMAGES_DIR}")

add_subdirectory(libs/core)
add_subdirectory(libs/filters)

add_executable(
    imagelite
    apps/cli/src/main.cpp
)

# 他不产生二进制文件，只是在构建时运行一些命令
add_custom_target(run 
    COMMAND imagelite blur ${SAMPLE_IMAGES_DIR}/input.jpg ${SAMPLE_IMAGES_DIR}/output.jpg 5
    DEPENDS imagelite
    WORKING_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}
    COMMENT "运行带参数的 CLI（命令行）工具"
) 

target_link_libraries(
    imagelite
    PRIVATE
        imagelite_core
        imagelite_filters
)

# target_include_directories(
#     imagelite
#     PRIVATE
#         libs/core/include
#         libs/filters/include
# )