# 构建一个静态库
add_library(
    imagelite_filters 
    STATIC
        src/filters.cpp
)

# 设置头文件搜索路径
# 作用有两个：
#  1. 本库自己编译时，能找到头文件
#  2. 任何链接 imagelite_filters 的 target，自动拥有这个 include 路径
target_include_directories(
    imagelite_filters 
    PUBLIC 
        ${CMAKE_CURRENT_SOURCE_DIR}/include
)

# 总结一句话：
#  target_link_libraries(A PUBLIC B)：A 的使用者，也必须看到 B
target_link_libraries(
    imagelite_filters
    PUBLIC 
        imagelite_core
)