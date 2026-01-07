# 构建静态库
# STATIC 表示构建静态库(.a 文件)，SHARED 表示构建动态库 (.so 文件)
add_library(
    imagelite_core 
    STATIC
    src/core.cpp
    src/image.cpp
)

# 为某个 target 设置头文件搜索路径
# 1. PRIVATE 仅本 target 自己用
# 2. PUBLIC 自己用 + 使用我的人也用
# 3. INTERFACE 自己不用，只给使用者
target_include_directories(
    imagelite_core 
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include
    PRIVATE
        ${PROJECT_SOURCE_DIR}/external/stb
)

# ${CMAKE_CURRENT_SOURCE_DIR} 表示当前 CMakeLists.txt 所在路径
message(STATUS "include 路径为: ${CMAKE_CURRENT_SOURCE_DIR}/include")
message(STATUS "stb 路径为: ${PROJECT_SOURCE_DIR}/external/stb")
