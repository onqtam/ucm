cmake_minimum_required(VERSION 2.8.12)

project(example)

include(../cmake/ucm.cmake)

set(UCM_UNITY_BUILD ON)

#set(CMAKE_CONFIGURATION_TYPES Debug Release)

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

#ucm_add_flags(-D_BONJOUR)
#ucm_add_flags(-D_HELLO CONFIG DEBUG RELEASE)
#ucm_add_linker_flags(-D_HOLA)
#ucm_add_linker_flags(-D_HALLO CONFIG DEBUG RELEASE)
#ucm_print_flags()

#message("")

if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    ucm_add_flags(CXX -fvisibility=hidden)
    ucm_add_flags(--save-temps CONFIG Debug)
endif()

if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
    ucm_add_flags(CXX /W3)
    ucm_add_flags(/W4 CONFIG Debug)
endif()

ucm_add_dirs("" TO sources)
ucm_add_files("separate/some1.cpp" "separate/some2.cpp" TO sources)
ucm_add_files("separate/some3.cpp" TO sources FILTER_POP 1)
ucm_add_dirs("util" TO sources RECURSIVE FILTER_POP 1)

ucm_remove_files("util/some_folder/source2.cpp" "util/some_folder/source3.cpp" FROM sources)

ucm_remove_directories("util/level1/level2" FROM sources MATCHES "deprecated")

ucm_include_file_in_sources("c.cc" HEADER "for_c.h")

# needed because of this: https://github.com/sakra/cotire/issues/89
ucm_include_file_in_sources("c.cc" HEADER "pch.h")

ucm_add_target(NAME ${PROJECT_NAME} TYPE EXECUTABLE UNITY CPP_PER_UNITY 3 PCH_FILE "pch.h" SOURCES "${sources}" UNITY_EXCLUDED "c.cc" "separate/some2.cpp")
