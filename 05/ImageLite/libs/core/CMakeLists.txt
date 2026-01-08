add_imagelite_module(imagelite_core TRUE
    src/core.cpp
    src/image.cpp
)

message(STATUS "include 路径为: ${CMAKE_CURRENT_SOURCE_DIR}/include")
message(STATUS "stb 路径为: ${PROJECT_SOURCE_DIR}/external/stb")
