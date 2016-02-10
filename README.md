ucm - useful cmake macros
-------------------------

[![Language](https://img.shields.io/badge/language-CMake-blue.svg)](https://github.com/onqtam/ucm/wiki)
[![Version](https://badge.fury.io/gh/onqtam%2Fucm.svg)](https://github.com/onqtam/ucm/releases)
[![License](http://img.shields.io/badge/license-MIT-blue.svg)](http://opensource.org/licenses/MIT)

Build status
------------

| Service            | Status                                                   |
|--------------------|----------------------------------------------------------|
| Travis-CI (Linux)  | [![Linux Status](https://travis-ci.org/onqtam/ucm.svg?branch=master)](https://travis-ci.org/onqtam/ucm)|
| Appveyor (Windows) | [![Windows Status](https://ci.appveyor.com/api/projects/status/m80f32y206l9tb52?svg=true)](https://ci.appveyor.com/project/onqtam/ucm)|

Todo
----

- test osx & get badge
- finish the documentation
- continue on https://github.com/sakra/cotire/issues/68

Documentation
-------------

##### <a name="menu"></a>Macros:

- [ucm_print_flags](#ucm_print_flags)
- [ucm_add_flags](#ucm_add_flags)
- [ucm_set_flags](#ucm_set_flags)
- [ucm_set_runtime](#ucm_set_runtime)
- [ucm_add_files](#ucm_add_files)
- [ucm_add_dirs](#ucm_add_dirs)
- [ucm_count_sources](#ucm_count_sources)
- [ucm_include_file_in_source](#ucm_include_file_in_source)
- [ucm_dir_list](#ucm_dir_list)
- [ucm_remove_files](#ucm_remove_files)
- [ucm_remove_directories](#ucm_remove_directories)
- [ucm_add_target](#ucm_add_target)

Macro notation: ```myMacro(NAME <name> [FLAG])``` - ```NAME``` and a name after it are mandatory and anything that is in [] is optional.

##### <a name="ucm_print_flags"></a>macro ```ucm_print_flags()```

Prints all relevant flags - for example with ```-DCMAKE_BUILD_TYPE=Debug``` given to cmake for makefiles:

```
CMAKE_C_FLAGS_DEBUG: -g
CMAKE_CXX_FLAGS_DEBUG: -g
CMAKE_C_FLAGS:  --save-temps -std=c++98 -pedantic -m64 -O2 -fvisibility=hidden
CMAKE_CXX_FLAGS:  --save-temps -std=c++98 -pedantic -m64 -O2 -fvisibility=hidden
```

or for a multi config generator like MSVC:

```
CMAKE_C_FLAGS_DEBUG: /D_DEBUG /MDd /Zi /Ob0 /Od /RTC1
CMAKE_CXX_FLAGS_DEBUG: /D_DEBUG /MDd /Zi /Ob0 /Od /RTC1
CMAKE_C_FLAGS_RELEASE: /MD /O2 /Ob2 /D NDEBUG
CMAKE_CXX_FLAGS_RELEASE: /MD /O2 /Ob2 /D NDEBUG
CMAKE_C_FLAGS:  /DWIN32 /D_WINDOWS /W3 /W4
CMAKE_CXX_FLAGS:  /DWIN32 /D_WINDOWS /W3 /GR /EHsc /W4
```

##### <a name="ucm_add_flags"></a>macro ```ucm_add_flags([C] [CPP] [CONFIG config] flag1 flag2 flag3...)```

Append the flags to a different set depending on it's options - examples:

```CMake
ucm_add_flags(-O3 -Wextra) # will add to CMAKE_C_FLAGS and CMAKE_CXX_FLAGS
ucm_add_flags(C -O3) # will add to CMAKE_C_FLAGS
ucm_add_flags(CXX -O3) # will add to CMAKE_CXX_FLAGS
ucm_add_flags(-O3 -Wall CONFIG Debug) # will add to CMAKE_C_FLAGS_DEBUG and CMAKE_CXX_FLAGS_DEBUG
```

##### <a name="ucm_set_flags"></a>macro ```ucm_set_flags([C] [CPP] [CONFIG <config>] flag1 flag2 flag3...)```

Removes the old and sets the new flags to a different set depending on it's options - examples:

```CMake
ucm_set_flags(CXX) # will clear CMAKE_CXX_FLAGS
ucm_set_flags() # will clear both CMAKE_C_FLAGS and CMAKE_CXX_FLAGS
ucm_set_flags(CXX -O3) # will set CMAKE_CXX_FLAGS
ucm_set_flags(-O3 -Wall CONFIG Debug) # will set CMAKE_C_FLAGS_DEBUG and CMAKE_CXX_FLAGS_DEBUG
```

##### <a name="ucm_set_runtime"></a>macro ```ucm_set_runtime([STATIC][DYNAMIC])```

Sets the runtime to static/dynamic - for example with MSVC as a generator:

```CMake
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

##### <a name="ucm_add_files"></a>macro ```ucm_add_files(RESULT <dest> [TRIM_FILTER <trim_filter>] src1 src2 scr3...)```

##### <a name="ucm_add_dirs"></a>macro ```ucm_add_dirs(RESULT <dest> [REC] [TRIM_FILTER <trim_filter>] dir1 dir2 dir3...)```

##### <a name="ucm_count_sources"></a>macro ```ucm_count_sources(<sources> <result>)```

##### <a name="ucm_include_file_in_source"></a>macro ```ucm_include_file_in_source(<source> <header>)```

##### <a name="ucm_dir_list"></a>macro ```ucm_dir_list(<dir> <result>)```

##### <a name="ucm_remove_files"></a>macro ```ucm_remove_files(RESULT <result> src1 src2 src3...)```

##### <a name="ucm_remove_directories"></a>macro ```ucm_remove_directories(RESULT <result> dir1 dir2 dir3...)```

##### <a name="ucm_add_target"></a>macro ```ucm_add_target(NAME <name> TYPE <type> SOURCES src1 src2 src3... [PCH_FILE <pch>] [UNITY [CPP_PER_UNITY <num>] [UNITY_EXCLUDED <excluded_list>]])```











