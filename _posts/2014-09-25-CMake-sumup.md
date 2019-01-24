---
layout: post
comments: true
title: "CMake sumup"
excerpt: "With CMake, cross-platformed code is so easy."
date: 2014-09-25 11:00:00
mathjax: true
---

<!-- add TOC here -->
<div id="genTocHere"></div>

## CMakeLists.txt for Lib project
```cmake
# add path of headers to include_directories
include_directories(include)

# STATIC for static lib, and SHARED for dynamic
FILE(GLOB SOURCES
	src/*.h
    src/*.cpp
)
add_library(mylib STATIC ${SOURCES})

# link libs, list all of them
target_link_libraries(mylib
    -lib-name-.lib
)
```

## CMakeLists.txt for EXE project
```cmake
# Set the minimum required version of cmake.
cmake_minimum_required(VERSION 2.6)

# Set the entire solution name.
project(testProject)

# Add path of headers to include_directories.
include_directories(include)
include_directories(lib/mylib)
include_directories(external/-external-lib-/include)

# Add an executable to the project using the specified source files.
file(GLOB SOURCES
    src/*.*
	lib/-external-lib-/source/*.h
    lib/-external-lib-/source/*.cpp
    test/main.cpp
)
add_executable(${PROJECT_NAME} ${SOURCES})

# add mylib lib project
add_subdirectory(lib/mylib)
target_link_libraries(${PROJECT_NAME} mylib)

# add opencv lib
find_package(OpenCV 2 REQUIRED)
include_directories(${OPENCV_INCLUDE_DIRS})
link_directories(${OPENCV_LIBRARY_DIRS})
add_definitions(${OPENCV_DEFINITIONS})
target_link_libraries(${PROJECT_NAME}${OpenCV_LIBS})
```

## Remarks
### Source files
- For **`add_executable()`**: actually there is no need to add headers here, but is essential to load them also in project view.
- To use name that contains spaces, use `\` before spaces to escape them.

### Group files
To group source files in VS (e.g. put given source files into new folder under VS), use
- `source_group(<name> FILES <src>...)` (use `${SOURCES}` for  files list defined earlier), or
- `source_group(<name> REGULAR_EXPRESSION <regex>)` (e.g. use `.*..*` for normal `*.*`)

### Group projects
```cmake
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set_target_properties(ProjectName PROPERTIES FOLDER FolderA/FolderB)
```

### Different libs for Debug/Release
```cmake
target_link_libraries(testProj
    debug -path-to-debug-lib-/lib_debug.lib
	optimized -path-to-release-lib-/lib.lib
)
```

### Check build target is Win32/64
```cmake
string(REGEX MATCH "Win64" ISWIN64 ${CMAKE_GENERATOR})
if("${ISWIN64}" STREQUAL "Win64")
	# is Win64...
else("${ISWIN64}" STREQUAL "Win64")
	# is Win32...
endif("${ISWIN64}" STREQUAL "Win64")
```

### Check VS version
After this, `${Platform_VS}` will be, e.g. `vc12` for `Visual Studio 2013`.

```cmake
string(REGEX REPLACE ".*Visual Studio ([0-9][0-9]).*" "vc\\1" Platform_VS ${CMAKE_GENERATOR})
```

### Set warning level
CMake for setting highest warning level can be:

```cmake
# set highest warnings level, and treat them as errors
if(MSVC)
    # c++
    if(CMAKE_CXX_FLAGS MATCHES "/W[0-4]")
        string(REGEX REPLACE "/W[0-4]" "/W4 /WX" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
    else()
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4 /WX")
    endif()

    # c
    if(CMAKE_C_FLAGS MATCHES "/W[0-4]")
        string(REGEX REPLACE "/W[0-4]" "/W4 /WX" CMAKE_C_FLAGS "${CMAKE_C_FLAGS}")
    else()
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /W4 /WX")
    endif()
elseif(CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_GNUCXX)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Werror -Wconversion -Wno-long-long -pedantic")
elseif(CMAKE_COMPILER_IS_GNUC)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Werror -Wconversion -Wno-long-long -pedantic")
endif()
```

To disable specific warnings[^1]<sup>, </sup>[^2]:

```cmake
add_definitions(-DNO_WARN_MBCS_MFC_DEPRECATION)  # disable MFC deprecation warnings
set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "/wd4819 /wd4996")   # disable warining C4819 and C4996
```

### Set compiler/linker options
> Note: `/Gm` is equivalent to `-Gm`, similar for all others.

```cmake
# set compiler options
set(CMAKE_MFC_FLAG 1)   # use static MFC: 1 for static MFC lib, 2 for shared
add_definitions("/D_CONSOLE /DUNICODE /D_UNICODE")   # set for both debug and release
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /Gm /RTC1 /MTd /ZI /TP")    # set only for debug mode
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /Oi /GL /MT /Gy /Zi")   # set only for release mode
```

Similarly, linker flags can be setup via the following macros

```
CMAKE_EXE_LINKER_FLAGS
CMAKE_EXE_LINKER_FLAGS_<CONFIG>
CMAKE_SHARED_LINKER_FLAGS
CMAKE_SHARED_LINKER_FLAGS_<CONFIG>
CMAKE_STATIC_LINKER_FLAGS
CMAKE_STATIC_LINKER_FLAGS_<CONFIG>
CMAKE_MODULE_LINKER_FLAGS
CMAKE_MODULE_LINKER_FLAGS_<CONFIG>
```

### Set SubSystem to "Windows"
```cmake
set_target_properties(${PROJECT_NAME} PROPERTIES LINK_FLAGS_DEBUG "/SUBSYSTEM:WINDOWS")
set_target_properties(${PROJECT_NAME} PROPERTIES LINK_FLAGS_RELEASE "/SUBSYSTEM:WINDOWS")
```

### Enable OpenMP
```cmake
# enable openmp
find_package(OpenMP)
if (OPENMP_FOUND)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
	set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
endif()
```

### Use MFC
```cmake
add_definitions(-D_AFXDLL)
set(CMAKE_MFC_FLAG 2)   # 1 for static MFC lib, 2 for shared
```

### Copy files
For copying all needed data files to binary dir (when `cmake`), there are two ways:
- List all the files, e.g.,

	```cmake
	file(GLOB files_needed
		test/test-data/*.*
		lib/-external-lib-/models/*.dat
		lib/-matlab-/*.m
	)
	file(COPY ${files_needed} DESTINATION ${CMAKE_CURRENT_BINARY_DIR})
	```
- List only the folders that contain the data, e.g.,

	```cmake
	file(GLOB files_needed
		test/test-data
		lib/-external-lib-/models
		lib/-matlab-
	)
	file(COPY ${files_needed} DESTINATION ${CMAKE_CURRENT_BINARY_DIR})
	```

The difference is that: the first method **only copy the listed files** to the binary dir **without directory structure**, while the second will **copy all the files in these folders**, as well as **with directory structure** (only the least parent will retain), i.e. there will be three folders named `test-data`, `models` and `-matlab-` in the binary dir after copying.

---
[^1]: Using CMake to setup VS: http://blog.csdn.net/xum2008/article/details/7268761
[^2]: Surpress MFC warnings: http://stackoverflow.com/a/25165654/2589776
