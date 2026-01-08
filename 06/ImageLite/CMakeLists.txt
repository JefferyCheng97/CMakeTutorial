cmake_minimum_required(VERSION 3.15)
project(ImageLite
    VERSION 1.0.2
    DESCRIPTION "一个展示 CMake 功能的简单图像处理库"
) 

# 进行属性设置
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# 把生成的 “可执行文件” 统一放到 build/bin/ 目录下
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
message(STATUS "可执行文件输出目录: ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}")

set(SAMPLE_IMAGES_DIR ${PROJECT_SOURCE_DIR}/sample_images)
message(STATUS "样例图片目录: ${SAMPLE_IMAGES_DIR}")

function(add_imagelite_module namne use_stb)
    add_library(${namne} STATIC ${ARGN})

    target_include_directories(
        ${namne}
        PUBLIC
            ${CMAKE_CURRENT_SOURCE_DIR}/include
    )    

    if(use_stb)
        target_include_directories(
            ${namne}
            PRIVATE
                ${PROJECT_SOURCE_DIR}/external/stb
        )
    endif()
endfunction()

# macro(add_imagelite_module namne use_stb)
#     set(MY_VAR "hello, i am my_var!!!!!!!!")
#     add_library(${namne} STATIC ${ARGN})

#     target_include_directories(
#         ${namne}
#         PUBLIC
#             ${CMAKE_CURRENT_SOURCE_DIR}/include
#     )    

#     if(${use_stb})
#         target_include_directories(
#             ${namne}
#             PRIVATE
#                 ${PROJECT_SOURCE_DIR}/external/stb
#         )
#     endif()
# endmacro()

# 警告: 这些 flags 不适用于 MSCV
# 只有在 GCC 或者 Clang 下才启用这些编译选项
set_directory_properties(
    PROPERTIES
    COMPILE_OPTIONS "-Wall;-Wextra"
)

add_subdirectory(libs/core)
add_subdirectory(libs/filters)

add_executable(
    imagelite
    apps/cli/src/main.cpp
)

# 创建自定义命令，生成构建信息文件 build_info.txt
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

# 在构建前复制 README.md 到 bin 目录
add_custom_command(
    TARGET imagelite
    PRE_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy
            "${CMAKE_SOURCE_DIR}/README.md"
            "${CMAKE_BINARY_DIR}/bin/README.md"
    COMMAND ${CMAKE_COMMAND} -E make_directory "${CMAKE_BINARY_DIR}/temp"
    COMMENT "正在设置构建环境..."
)

add_custom_command(
    TARGET imagelite
    POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E rm -rf "${CMAKE_BINARY_DIR}/temp"
    COMMENT "构建后清理临时文件..."
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