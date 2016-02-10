cmake_minimum_required(VERSION 2.8.12)

project(example)

include(../cmake/ucm.cmake)

set(UCM_UNITY_BUILD ON)

# testing setting the runtime back and forth and leaving it static
ucm_set_runtime(STATIC)
ucm_print_flags()
message("")
ucm_set_runtime(DYNAMIC)
ucm_print_flags()
message("")
ucm_set_runtime(STATIC)
ucm_print_flags()
message("")

if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    ucm_add_flags(CXX -fvisibility=hidden)
    ucm_add_flags(--save-temps CONFIG Debug)
endif()

if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
    ucm_add_flags(CXX /W3)
    ucm_add_flags(/W4 CONFIG Debug)
endif()

ucm_add_dirs("" RESULT sources)
ucm_add_files("separate/some1.cpp" "separate/some2.cpp" RESULT sources)
ucm_add_files("separate/some3.cpp" RESULT sources TRIM_FILTER 1)
ucm_add_dirs("util" RESULT sources REC TRIM_FILTER 1)

ucm_remove_files("util/some_folder/source2.cpp" "util/some_folder/source3.cpp" RESULT sources)

ucm_remove_directories("util/level1/level2" RESULT sources)

ucm_include_file_in_source("c.cc" "for_c.h")

# needed because of this: https://github.com/sakra/cotire/issues/68
ucm_include_file_in_source("c.cc" "pch.h")

ucm_add_target(NAME ${PROJECT_NAME} TYPE EXECUTABLE UNITY CPP_PER_UNITY 3 PCH_FILE "pch.h" SOURCES "${sources}" UNITY_EXCLUDED "c.cc" "separate/some2.cpp")
