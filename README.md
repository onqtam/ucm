ucm - useful cmake macros
-------------------------

[![Language](https://img.shields.io/badge/language-CMake-blue.svg)](https://github.com/onqtam/ucm/wiki)
[![Version](https://badge.fury.io/gh/onqtam%2Fucm.svg)](https://github.com/onqtam/ucm/releases)
[![License](http://img.shields.io/badge/license-MIT-blue.svg)](http://opensource.org/licenses/MIT)

[cotire](https://github.com/sakra/cotire) is an optional submodule for some macros and in order for them to fully work either do ```git submodule update --init``` after cloning or include cotire in your cmake files before ucm.

Tested with MSVC/ICC/GCC/Clang.

Contribution of new macros is welcome!

Build status
------------

| Service               | Status |
|-----------------------|--------|
| Travis-CI (Linux/OSX) | [![Linux/OSX Status](https://travis-ci.org/onqtam/ucm.svg?branch=master)](https://travis-ci.org/onqtam/ucm)|
| Appveyor (Windows)    | [![Windows Status](https://ci.appveyor.com/api/projects/status/m80f32y206l9tb52?svg=true)](https://ci.appveyor.com/project/onqtam/ucm)|

Documentation
-------------

##### <a name="macros"></a>Macros:

- [ucm_print_flags](#ucm_print_flags)
- [ucm_add_flags](#ucm_add_flags)
- [ucm_set_flags](#ucm_set_flags)
- [ucm_set_runtime](#ucm_set_runtime)
- [ucm_add_files](#ucm_add_files)
- [ucm_add_dirs](#ucm_add_dirs)
- [ucm_count_sources](#ucm_count_sources)
- [ucm_include_file_in_sources](#ucm_include_file_in_sources)
- [ucm_dir_list](#ucm_dir_list)
- [ucm_remove_files](#ucm_remove_files)
- [ucm_remove_directories](#ucm_remove_directories)
- [ucm_add_target](#ucm_add_target)

Macro notation: ```myMacro(NAME <name> [FLAG])``` - ```NAME``` and a name after it are required and anything that is in [] is optional.

##### <a name="ucm_print_flags"></a>macro ```ucm_print_flags()```
<hr>

Prints all relevant flags - for example with ```-DCMAKE_BUILD_TYPE=Debug``` given to cmake for makefiles:

```
CMAKE_C_FLAGS_DEBUG: -g
CMAKE_CXX_FLAGS_DEBUG: -g
CMAKE_C_FLAGS:  --save-temps -std=c++98 -pedantic -m64 -O2 -fvisibility=hidden
CMAKE_CXX_FLAGS:  --save-temps -std=c++98 -pedantic -m64 -O2 -fvisibility=hidden
```

or for a multi config generator like Visual Studio:

```
CMAKE_C_FLAGS_DEBUG: /D_DEBUG /MDd /Zi /Ob0 /Od /RTC1
CMAKE_CXX_FLAGS_DEBUG: /D_DEBUG /MDd /Zi /Ob0 /Od /RTC1
CMAKE_C_FLAGS_RELEASE: /MD /O2 /Ob2 /D NDEBUG
CMAKE_CXX_FLAGS_RELEASE: /MD /O2 /Ob2 /D NDEBUG
CMAKE_C_FLAGS:  /DWIN32 /D_WINDOWS /W3 /W4
CMAKE_CXX_FLAGS:  /DWIN32 /D_WINDOWS /W3 /GR /EHsc /W4
```

##### <a name="ucm_add_flags"></a>macro ```ucm_add_flags([C] [CXX] [CONFIG config] flag1 flag2 flag3...)```

Append the flags to a different set depending on it's options - examples:

```cmake
ucm_add_flags(-O3 -Wextra) # will add to CMAKE_C_FLAGS and CMAKE_CXX_FLAGS
ucm_add_flags(C -O3) # will add to CMAKE_C_FLAGS
ucm_add_flags(CXX -O3) # will add to CMAKE_CXX_FLAGS
ucm_add_flags(-O3 -Wall CONFIG Debug) # will add to CMAKE_C_FLAGS_DEBUG and CMAKE_CXX_FLAGS_DEBUG
```

##### <a name="ucm_set_flags"></a>macro ```ucm_set_flags([C] [CXX] [CONFIG <config>] flag1 flag2 flag3...)```

Removes the old and sets the new flags to a different set depending on it's options - examples:

```cmake
ucm_set_flags(CXX) # will clear CMAKE_CXX_FLAGS
ucm_set_flags() # will clear both CMAKE_C_FLAGS and CMAKE_CXX_FLAGS
ucm_set_flags(CXX -O3) # will set CMAKE_CXX_FLAGS
ucm_set_flags(-O3 -Wall CONFIG Debug) # will set CMAKE_C_FLAGS_DEBUG and CMAKE_CXX_FLAGS_DEBUG
```

##### <a name="ucm_set_runtime"></a>macro ```ucm_set_runtime([STATIC][DYNAMIC])```

Sets the runtime to static/dynamic - for example with Visual Studio as a generator:

```cmake
ucm_print_flags()
ucm_set_runtime(STATIC)
ucm_print_flags()
```

will result in:

```
CMAKE_C_FLAGS_DEBUG: /D_DEBUG /MDd /Zi /Ob0 /Od /RTC1
CMAKE_CXX_FLAGS_DEBUG: /D_DEBUG /MDd /Zi /Ob0 /Od /RTC1
CMAKE_C_FLAGS_RELEASE: /MD /O2 /Ob2 /D NDEBUG
CMAKE_CXX_FLAGS_RELEASE: /MD /O2 /Ob2 /D NDEBUG
CMAKE_C_FLAGS:  /DWIN32 /D_WINDOWS /W3 /W4
CMAKE_CXX_FLAGS:  /DWIN32 /D_WINDOWS /W3 /GR /EHsc /W4

CMAKE_C_FLAGS_DEBUG: /D_DEBUG /MTd /Zi /Ob0 /Od /RTC1
CMAKE_CXX_FLAGS_DEBUG: /D_DEBUG /MTd /Zi /Ob0 /Od /RTC1
CMAKE_C_FLAGS_RELEASE: /MT /O2 /Ob2 /D NDEBUG
CMAKE_CXX_FLAGS_RELEASE: /MT /O2 /Ob2 /D NDEBUG
CMAKE_C_FLAGS:  /DWIN32 /D_WINDOWS /W3 /W4
CMAKE_CXX_FLAGS:  /DWIN32 /D_WINDOWS /W3 /GR /EHsc /W4
```

##### <a name="ucm_add_files"></a>macro ```ucm_add_files(src1 src2 scr3... TO <sources> [FILTER_POP <num>])```

Adds the sources to the sources variable and sets up filters for the solution explorer of Visual Studio (probably for XCode/CodeBlocks too).

The filters will mimic the filesystem - if we have given ```dir1/test/a.cpp``` we would have by default ```dir1/test``` as nested filters in the solution explorer. This can be controlled with ```FILTER_POP``` - 1 would result in only ```test``` as a filter and 2 would result in no filter for ```a.cpp``` - see [ucm_add_dirs](#ucm_add_dirs) for a visual example.

##### <a name="ucm_add_dirs"></a>macro ```ucm_add_dirs(dir1 dir2 dir3... TO <sources> [RECURSIVE] [FILTER_POP <NUM>])```

Adds all sources (sources and headers with all valid c/c++ extensions) from the directories given.

Can be recursive with the ```RECURSIVE``` flag.

Like ```ucm_add_files()``` filters for the solution explorer of IDEs can be controlled with ```FILTER_POP``` - example:

| CMake code                                       | result                           |
|--------------------------------------------------|----------------------------------|
| ```ucm_add_dirs(util TO sources)```              | ![0](test/doc_data/filter_0.png) |
| ```ucm_add_dirs(util TO sources FILTER_POP 1)``` | ![1](test/doc_data/filter_1.png) |

##### <a name="ucm_count_sources"></a>macro ```ucm_count_sources(src1 src2 src3... RESULT <result>)```

Given a list of sources - returns the number of source files (no headers - only valid source extensions) in the result.

```
set(sources "a.cpp;b.cpp;h.hpp")
ucm_count_sources(${sources} c.cpp d.cpp RESULT res) # would return 4 in res
```

##### <a name="ucm_include_file_in_sources"></a>macro ```ucm_include_file_in_sources(src1 src2 src3... HEADER <header>)```

Includes the header in the source file with a compile flag (without modifying the file) either with ```-include "hdr.h"``` or with ```/FI"hdr.h"``` depending on the compiler.

##### <a name="ucm_dir_list"></a>macro ```ucm_dir_list(<thedir> <result>)```

Returns a list of subdirectories for a given directory.

##### <a name="ucm_remove_files"></a>macro ```ucm_remove_files(src1 src2 src3... FROM <sources>)```

Removes the given source files from the sources list - example:

```CMake
ucm_add_dirs(utils REC TO sources)
ucm_remove_files(utils/deprecated.h FROM sources)
```

##### <a name="ucm_remove_directories"></a>macro ```ucm_remove_directories(dir1 dir2 dir3... FROM <sources>)```

Removes all source files from the given directories from the sources list - example:

```CMake
ucm_add_dirs(utils REC TO sources)
ucm_remove_directories(utils/deprecated utils/experimental FROM sources)
```

##### <a name="ucm_add_target"></a>macro ```ucm_add_target(NAME <name> TYPE <EXECUTABLE|STATIC|SHARED|MODULE> SOURCES src1 src2 src3... [PCH_FILE <pch>] [UNITY [CPP_PER_UNITY <num>] [UNITY_EXCLUDED excl_src1 excl_src2 ...]])```

A wrapper of ```add_library()``` and ```add_executable()``` calls. Uses [cotire](https://github.com/sakra/cotire) for platform/compiler independent usage of a precompiled header and/or making a unity build of the target. For information about unity builds in general go to the cotire page. A unity-enabled target may get built up to 90% faster than a normal one.

```CMake
ucm_add_target(NAME example TYPE EXECUTABLE SOURCES "${sources}" UNITY UNITY_EXCLUDED "separate/some2.cpp")
```

When the ```UCM_UNITY_BUILD``` ucm option is set to ```ON``` (```OFF``` by default) a target registered like in the example above will actually result in 2 targets added - the unity target with ```example``` as a name (included in the build by default) and the original target with ```example_ORIGINAL``` as a name (excluded from the build by default). This allows the user to browse and modify the sources in the original target properly within the IDE.

When new sources are added to the original target the unity target will be updated accordingly by cotire.

Targets can be excluded from unity builds by adding them in the ```UCM_UNITY_BUILD_EXCLUDE_TARGETS``` list when invoking cmake (handy if a target becomes problematic in a unity build or if you want to iterate fast on a particular target and want to compile it's sources separately).

To explicitly say how many source files should go into a unity source use CPP_PER_UNITY (default is 100). Another option is to pass not a number but ```-jX``` after ```CPP_PER_UNITY``` and that would mean dividing the sources into X unity sources.

UNITY_EXCLUDED - list of files from the target that should be excluded from unify-ing (will be used normally by themselves - can be used to fix compilation errors).

PCH_FILE - a header file that will be precompiled and prefixed to all sources (they won't even need to explicitly ```#include``` it).

Unity examples - given 100 .cpp files in the target:

- ```CPP_PER_UNITY 5``` would mean 20 .cxx unity files including 5 of the original .cpp files each
- ```CPP_PER_UNITY 10``` would mean 10 .cxx unity files including 10 of the original .cpp files each
- ```CPP_PER_UNITY -j8``` would mean 8 .cxx unity files dividing the original 100 among them

How it looks in the IDE:

![1](test/doc_data/unity.png)






