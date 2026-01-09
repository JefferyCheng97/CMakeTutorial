# 寻找测试框架
find_package(GTest REQUIRED)

# core 模块的测试
add_executable(
    core_tests
    core_tests/test_image.cpp
    core_tests/test_core.cpp
)

target_link_libraries(
    core_tests
    PRIVATE
        imagelite_core
        GTest::GTest
        GTest::Main
)

# filters 模块的测试
add_executable(
    filters_tests
    filters_tests/test_filters.cpp
)

target_link_libraries(
    filters_tests
    PRIVATE
        imagelite_core
        imagelite_filters
        GTest::GTest
        GTest::Main
)

# 注册测试
add_test(NAME CoreTests COMMAND core_tests)
add_test(NAME FiltersTests COMMAND filters_tests)