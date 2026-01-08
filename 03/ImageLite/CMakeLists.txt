cmake_minimum_required(VERSION 3.15)
project(ImageLite
    VERSION 1.0.2
    DESCRIPTION "一个展示 CMake 功能的简单图像处理库"
) 

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

add_custom_command(
    OUTPUT "${CMAKE_BINARY_DIR}/build_info.txt"
    COMMAND ${CMAKE_COMMAND} -E echo "ImageLite Build Information" > "${CMAKE_BINARY_DIR}/build_info.txt"
    COMMAND ${CMAKE_COMMAND} -E echo "CMake Version: ${CMAKE_VERSION}" >> "${CMAKE_BINARY_DIR}/build_info.txt"
    COMMAND ${CMAKE_COMMAND} -E echo "C++ Compiler: ${CMAKE_CXX_COMPILER_ID} ${CMAKE_CXX_COMPILER_VERSION}" >> "${CMAKE_BINARY_DIR}/build_info.txt"
    COMMAND ${CMAKE_COMMAND} -E echo "Project Version: ${PROJECT_VERSION}" >> "${CMAKE_BINARY_DIR}/build_info.txt"
    COMMAND ${CMAKE_COMMAND} -E echo "Project Description: ${PROJECT_DESCRIPTION}" >> "${CMAKE_BINARY_DIR}/build_info.txt"
    COMMENT "生成构建信息文件 build_info.txt"
    VERBATIM
)

# 这里使用 ALL 选项，不再需要 --target build_info，构建时会自动生成
add_custom_target(
    build_info ALL
    DEPENDS "${CMAKE_BINARY_DIR}/build_info.txt"
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