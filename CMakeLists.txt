cmake_minimum_required(VERSION 3.10)
project(JivanLog)

include(cmake/utils.cmake)

#显示详细的原始编译信息，主要用于定位一些链接错误，看看库路径什么的是否配置对。
set(CMAKE_VERBOSE_MAKEFILE ON)

# 指定编译选项
set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -std=c++11 -O0 -ggdb -Wall -Werror")

# -Wno-unused-function: 不要警告未使用函数
# -Wno-builtin-macro-redefined: 不要警告内置宏重定义，用于重定义内置的__FILE__宏
# -Wno-deprecated: 不要警告过时的特性
# -Wno-deprecated-declarations: 不要警告使用带deprecated属性的变量，类型，函数
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS} -Wno-unused-function -Wno-builtin-macro-redefined -Wno-deprecated -Wno-deprecated-declarations")

set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -std=c++11 -O3 -Wall")

include_directories(${PROJECT_SOURCE_DIR}/inlcude)

option(BUILD_TEST "ON for complile test" ON)

find_package(Boost REQUIRED)
if (Boost_FOUND)
    include_directories(${Boost_INCLUDE_DIRS})
endif ()

set(LIB_SRC
        code/log.cpp
        code/mutex.cpp
        code/util.cpp
        )
add_library(log SHARED ${LIB_SRC})

force_redefine_file_macro_for_sources(log)

set(LIBS
        log
        pthread
        )

if (BUILD_TEST)
    jivan_add_executable(test_log "tests/test_log.cpp" log "${LIBS}")
endif ()

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)