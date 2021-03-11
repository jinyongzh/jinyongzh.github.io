---
title: OpenCV加入CMake工程编译
mathjax: false
date: 2021-01-18 09:40:00
tags:
- OpenCV
categories:
- Programming
---

## OpenCV使用
&ensp;&ensp;&ensp;&ensp;{% post_link 编译OpenCV4-5-1和Contrib库 false %}介绍了MSVC编译OpenCV，编译出来的库是导入VS使用，这里要在CMake工程中使用，用原来编译的库即可。另外，CUDA在Windows上只支持MSVC
<!-- more -->

### OpenCV加入CMake工程
&ensp;&ensp;&ensp;&ensp;把编译好的OpenCV文件放到libs目录下，编译CMakeLists.txt就行。因为OpenCV库文件放在工程中，没有加入系统Path，最后一步需要拷贝对应的动态链接库到执行目录下，不然会报错找不到动态链接库

```cmake
# OpenCV Path
set(OpenCV_CMAKE_DIR "libs/OpenCV")
set(OpenCV_CMAKE_LIB_DIR "${CMAKE_CURRENT_SOURCE_DIR}/${OpenCV_CMAKE_DIR}/x64/vc16/bin")
include_directories("${OpenCV_CMAKE_DIR}/include" "${OpenCV_CMAKE_DIR}/include/opencv2")

if(CMAKE_BUILD_TYPE  MATCHES "Debug")
    set(OpenCV_CMAKE_LIB_NAME "libopencv_world451d.dll")
else()
    set(OpenCV_CMAKE_LIB_NAME "libopencv_world451.dll")
endif()

# OpenCV Library
set(OpenCV_DIR "../${OpenCV_CMAKE_DIR}")
find_package(OpenCV REQUIRED)

add_executable(Rasterizer main.cpp rasterizer.hpp rasterizer.cpp Triangle.hpp Triangle.cpp)

# 链接
target_link_libraries(Rasterizer ${OpenCV_LIBRARIES})

# 拷贝动态链接库到执行目录
add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy
        "${OpenCV_CMAKE_LIB_DIR}/${OpenCV_CMAKE_LIB_NAME}"
        $<TARGET_FILE_DIR:${PROJECT_NAME}>)
```
### VSCode测试
&ensp;&ensp;&ensp;&ensp;安装好CMake插件后，选择VS的编译工具，然后生成成功。有一句提示让我们把库所在的路径加入Path，不过已经拷贝了，就没必要，直接运行

```cmake
[cmake] -- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.19042.
[cmake] -- OpenCV ARCH: x64
[cmake] -- OpenCV RUNTIME: vc16
[cmake] -- OpenCV STATIC: OFF
[cmake] -- Found OpenCV 4.5.1 in D:/Code/Graphics/GAMES101/libs/OpenCV/x64/vc16/lib
[cmake] -- You might need to add D:\Code\Graphics\GAMES101\libs\OpenCV\x64\vc16\bin to your PATH to be able to run your applications
[cmake] -- Configuring done
[cmake] -- Generating done
[cmake] -- Build files have been written to: D:/Code/Graphics/GAMES101/build
```