# Copyright (C) 2014-present prototyped.cn All rights reserved.
# Distributed under the terms and conditions of the Apache License. 
# See accompanying files LICENSE.

cmake_minimum_required(VERSION 2.8)
project (CppBench)

set(PROJECT_ROOT_DIR ${CMAKE_SOURCE_DIR})
set(PROJECT_SRC_DIR ${PROJECT_ROOT_DIR}/src)

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_ROOT_DIR}/bin)

if(WIN32)
    add_definitions(-D_WIN32_WINNT=0x0601)
    add_definitions(-DNOMINMAX)
    add_definitions(-D_SCL_SECURE_NO_WARNINGS)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
elseif(UNIX)
    add_definitions(-D__STDC_LIMIT_MACROS)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -std=c++11")
endif()

file(GLOB_RECURSE LIB_HEADER_FILES src/*.h)
file(GLOB_RECURSE LIB_SOURCE_FILES src/*.cpp)

include_directories(
    ${PROJECT_ROOT_DIR}/src
)

macro(SOURCE_GROUP_BY_DIR source_files)
    set(sgbd_cur_dir ${CMAKE_CURRENT_SOURCE_DIR})
    foreach(sgbd_file ${${source_files}})
        string(REGEX REPLACE ${sgbd_cur_dir}/\(.*\) \\1 sgbd_fpath ${sgbd_file})
        string(REGEX REPLACE "\(.*\)/.*" \\1 sgbd_group_name ${sgbd_fpath})
        string(COMPARE EQUAL ${sgbd_fpath} ${sgbd_group_name} sgbd_nogroup)
        string(REPLACE "/" "\\" sgbd_group_name ${sgbd_group_name})
        if(sgbd_nogroup)
            set(sgbd_group_name "\\")
        endif(sgbd_nogroup)
        source_group(${sgbd_group_name} FILES ${sgbd_file})
    endforeach(sgbd_file)
endmacro(SOURCE_GROUP_BY_DIR)

SOURCE_GROUP_BY_DIR(LIB_HEADER_FILES)
SOURCE_GROUP_BY_DIR(LIB_SOURCE_FILES)

add_library(libbench ${LIB_HEADER_FILES} ${LIB_SOURCE_FILES})
add_subdirectory(test) 
