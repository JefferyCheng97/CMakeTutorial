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
set(SAMPLE_IMAGES_DIR ${PROJECT_SOURCE_DIR}/sample_images)

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

    # 设置代码的编译选项
    # Wall 和 Wextra 各自针对不同的代码警告级别
    target_compile_options(
        ${namne}
        PRIVATE
            $<$<CXX_COMPILER_ID:GNU>:-Wall;-Wextra>
            $<$<CXX_COMPILER_ID:Clang>:-Wall;-Wextra>
            $<$<CXX_COMPILER_ID:MSVC>:/W4>
    )

    # 平台相关的宏定义
    # 如果代码头文件中有 #ifdef，那就需要用到 target_compile_definitions
    target_compile_definitions(
        ${namne}
        PRIVATE
            $<$<PLATFORM_ID:Windows>:IMAGELITE_WINDOWS>
            $<$<PLATFORM_ID:Linux>:IMAGELITE_LINUX>
            $<$<PLATFORM_ID:Darwin>:IMAGELITE_MACOS>
    )

    # 调试模式下的额外编译选项
    # 对于 GNU 和 Clang，启用更严格警告
    # 对于 MSVC，启用调试信息且禁用优化
    target_compile_options(
        ${namne}
        PRIVATE
            $<$<AND:$<CONFIG:Debug>,$<CXX_COMPILER_ID:GNU>>:-Wpedantic -g>
            $<$<AND:$<CONFIG:Debug>,$<CXX_COMPILER_ID:Clang>>:-Wpedantic -g>
            $<$<AND:$<CONFIG:Debug>,$<CXX_COMPILER_ID:MSVC>>:/Zi /Od>
    )

    # 发布模式下的优化选项
    target_compile_options(
        ${namne}
        PRIVATE
            $<$<AND:$<CONFIG:Release>,$<OR:$<CXX_COMPILER_ID:GNU>,
            $<CXX_COMPILER_ID:Clang>>>:-O3>
            $<$<AND:$<CONFIG:Release>,$<CXX_COMPILER_ID:MSVC>>:/O2>
    )
endfunction()

# 目录属性，会影响目录及子目录下所有目标
# set_directory_properties(
#     PROPERTIES
#     COMPILE_OPTIONS "$<$<OR:$<CXX_COMPILER_ID:GNU>,$<CXX_COMPILER_ID:Clang>>:-Wall;-Wextra>;
#     $<$<CXX_COMPILER_ID:MSVC>:/W4>"
# )

add_subdirectory(libs/core)
add_subdirectory(libs/filters)

add_executable(
    imagelite
    apps/cli/src/main.cpp
)

set_target_properties(
    imagelite
    PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY_DEBUG "${CMAKE_BINARY_DIR}/bin/debug"
    RUNTIME_OUTPUT_DIRECTORY_RELEASE "${CMAKE_BINARY_DIR}/bin/release"

)

target_link_libraries(
    imagelite 
    PRIVATE
        imagelite_core
        imagelite_filters
)

# 外部的这些 target_compile_options 其实是作用在 imagelite 这个 target 上的
# function 中的 target_compile_options 是作用在各个模块 target 上的，也就是 core 和 filters 上的
target_compile_options(
    imagelite
    PRIVATE
        $<$<CXX_COMPILER_ID:GNU>:-Wall -Wextra -Wpedantic -Wconversion -Wshadow>
        $<$<CXX_COMPILER_ID:Clang>:-Wall -Wextra -Wpedantic -Wconversion -Wshadow>
        $<$<CXX_COMPILER_ID:MSVC>:/W4 /WX>
)

# debug 模式下的编译选项
target_compile_options(
    imagelite
    PRIVATE
        $<$<AND:$<CONFIG:Debug>,$<OR:$<CXX_COMPILER_ID:GNU>,$<CXX_COMPILER_ID:Clang>>>:-g -O0>
        $<$<AND:$<CONFIG:Debug>,$<CXX_COMPILER_ID:MSVC>>:/Zi /Od /RTC1>
)

# release 模式下的编译选项
target_compile_options(
    imagelite
    PRIVATE
        $<$<AND:$<CONFIG:Release>,$<OR:$<CXX_COMPILER_ID:GNU>,$<CXX_COMPILER_ID:Clang>>>:-O3>
        $<$<AND:$<CONFIG:Release>,$<CXX_COMPILER_ID:MSVC>>:/O2>
)

target_include_directories(
    imagelite
    PRIVATE
        libs/core/include
        libs/filters/include
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

# 他不产生二进制文件，只是在构建时运行一些命令
add_custom_target(run 
    COMMAND imagelite blur ${SAMPLE_IMAGES_DIR}/input.jpg ${SAMPLE_IMAGES_DIR}/output.jpg 5
    DEPENDS imagelite
    WORKING_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}
    COMMENT "运行带参数的 CLI（命令行）工具"
)