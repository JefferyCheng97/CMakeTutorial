# 构建静态库
# STATIC 表示构建静态库(.a 文件)，SHARED 表示构建动态库 (.so 文件)
add_library(
    imagelite_core 
    STATIC
    src/core.cpp
    src/image.cpp
)

# 设置 imagelite_core 的头文件搜索路径
#   PUBLIC: 对外暴露的 API 头文件目录
#           会传递给所有依赖 imagelite_core 的 target
#   PRIVATE: 仅供本库实现使用的内部依赖
#            不会暴露给下游（如 filters / app）
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
