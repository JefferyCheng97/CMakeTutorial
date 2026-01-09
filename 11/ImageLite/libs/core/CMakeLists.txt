include(FetchContent)

# 之所以将 FetchContent 放在当前 CMakeLists.txt 中，而不是顶层 CMakeLists.txt，
# 是因为 core 库依赖 stb 库，而其他模块不需要这个依赖
# 这种局部化管理方式特别适合仅被项目耽搁组件使用的依赖项
# 对于被多个模块共享的依赖项，建议放在顶层 CMakeLists.txt 中进行管理
FetchContent_Declare(
    stb
    GIT_REPOSITORY https://github.com/nothings/stb.git
    GIT_TAG master
)

FetchContent_GetProperties(stb)

# 如果 stb 库还没有被获取，则进行获取和配置
if(NOT stb_POPULATED)
    FetchContent_Populate(stb)

    add_library(stb_image INTERFACE)

    target_include_directories(
        stb_image 
        INTERFACE
        ${stb_SOURCE_DIR}
    )
endif()

add_imagelite_module(
    imagelite_core
    src/core.cpp
    src/image.cpp
)

target_link_libraries(
    imagelite_core
    PRIVATE    
    stb_image
)

set_target_properties(
    imagelite_core
    PROPERTIES
    POSITION_INDEPENDENT_CODE ON
    CXX_VISIBILITY_PRESET hidden
)

get_target_property(VISIBILITY imagelite_core CXX_VISIBILITY_PRESET)
message("Visibility for imagelite_core: ${VISIBILITY}")

target_compile_definitions(imagelite_core PRIVATE
    $<$<CONFIG:Debug>:IMAGELITE_DEBUG>
    $<$<CONFIG:Debug>:IMAGELITE_ENABLE_LOGGING>
    $<$<CONFIG:Release>:IMAGELITE_RELEASE>
)

