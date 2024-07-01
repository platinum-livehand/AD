## 使用 vcpkg 安装第三方开源库

```powershell
PS C:\vcpkg> vcpkg install eigen3:x64-mingw-static
Computing installation plan...
The following packages are already installed:
    eigen3:x64-mingw-static@3.4.0#4
eigen3:x64-mingw-static is already installed
Total install time: 375 us
eigen3 provides CMake targets:

  # this is heuristically generated, and may not be correct
  find_package(Eigen3 CONFIG REQUIRED)
  target_link_libraries(main PRIVATE Eigen3::Eigen)

eigen3 provides pkg-config modules:

  # A C++ template library for linear algebra: vectors, matrices, and related algorithms
  eigen3
```

​	通过 `vcpkg intall eigen3:x64-mingw-static` 编译安装 Eigen3，将 `C:\vcpkg\packages` 文件夹下的 `eigen3_x64-mingw-static`文件夹移植到 `third_party` 文件夹下，不同于全局使用同一库，不需要设置众多的环境变量，具有较高的移植性。

​	在 CMake 中的使用方法为：

```cmake
# 配置 Eigen3
set(Eigen3_DIR ${CMAKE_SOURCE_DIR}/third_party/eigen3_x64-mingw-static/share/eigen3)
find_package(Eigen3 CONFIG REQUIRED)
target_link_libraries(main PRIVATE Eigen3::Eigen)
```

## 项目结构

```powershell
cmake_planning_demo/
│  CMakeLists.txt
│  
├─bin
│      libpnc_map.dll
│      libprocess.dll
│      libshow_result.dll
│      planning_main.exe
│   
├─build
├─docs
├─lib
├─src
│  │  CMakeLists.txt
│  │  planning_main.cpp
│  │
│  ├─pnc_map
│  │      CMakeLists.txt
│  │      pnc_map.cpp
│  │      pnc_map.h
│  │
│  ├─process
│  │      CMakeLists.txt
│  │      process.cpp
│  │      process.h
│  │
│  └─show_result
│         CMakeLists.txt
│         show_result.cpp
│         show_result.h
│
├─test
└─third_party
    ├─EasyX
    │  ├─include
    │  ├─lib-for-devcpp_5.4.0
    │  ├─lib32
    │  └─lib64
    └─eigen3_x64-mingw-static
        ├─debug
        ├─include
        ├─lib
        └─share
            └─eigen3
                    copyright
                    Eigen3Config.cmake
                    Eigen3ConfigVersion.cmake
                    Eigen3Targets.cmake
                    UseEigen3.cmake
                    vcpkg.spdx.json
                    vcpkg_abi_info.txt
```

## CMakeLists.txt

### 根目录

```cmake
cmake_minimum_required(VERSION 3.29.0)

project(planning
    VERSION 0.0.1
    DESCRIPTION "a demo of cmake planning"
    HOMEPAGE_URL "https://github.com/platinum-livehand/cmake_planning_demo"
    LANGUAGES CXX
)

# 检测操作系统
if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
    message(STATUS "Configuring on/for Linux")
elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
    message(STATUS "Configuring on/for macOS")
elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows")
    message(STATUS "Configuring on/for Windows")
elseif(CMAKE_SYSTEM_NAME STREQUAL "AIX")
    message(STATUS "Configuring on/for IBM AIX")
else()
    message(STATUS "Configuring on/for ${CMAKE_SYSTEM_NAME}")
endif()

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
message(STATUS "CXX_STANDARD = ${CMAKE_CXX_STANDARD}")

# 静态库输出目录
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/lib)
# 动态库输出目录
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/bin)
# 可执行程序输出目录
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)

# 目录变量
set(PROCESS_DIR ${CMAKE_SOURCE_DIR}/src/process)
set(PNC_MAP_DIR ${CMAKE_SOURCE_DIR}/src/pnc_map)
set(SHOW_RESULT_DIR ${CMAKE_SOURCE_DIR}/src/show_result)
set(EASYX_DIR ${CMAKE_SOURCE_DIR}/third_party/EasyX)

# 配置 Eigen3
set(Eigen3_DIR ${CMAKE_SOURCE_DIR}/third_party/eigen3_x64-mingw-static/share/eigen3)
find_package(Eigen3 CONFIG REQUIRED)

# 添加子目录
add_subdirectory(src)

# # 启动测试
# enable_testing()
# add_subdirectory(test/map_test)
```

### src 目录

```cmake
project(planning_main)

add_subdirectory(pnc_map)
add_subdirectory(process)
add_subdirectory(show_result)

# 生成可执行程序
add_executable(${PROJECT_NAME} planning_main.cpp)

# 添加包含
target_include_directories(${PROJECT_NAME}
PUBLIC
${PROCESS_DIR}
${SHOW_RESULT_DIR}
)

# 链接库
target_link_libraries(${PROJECT_NAME}
PUBLIC
process
show_result
)
```

### pnc_map 目录

```cmake
project(pnc_map)

# 生成动态库
add_library(${PROJECT_NAME}
SHARED
pnc_map.cpp
)
```

### process 目录

```cmake
project(process)

# 生成动态库
add_library(${PROJECT_NAME}
SHARED
process.cpp
)

# 添加包含
target_include_directories(${PROJECT_NAME}
PUBLIC
${PNC_MAP_DIR}
)

# 链接库
target_link_libraries(${PROJECT_NAME}
PUBLIC
pnc_map
Eigen3::Eigen
)
```

### show_result 目录

```cmake
project(show_result)

# 生成动态库
add_library(${PROJECT_NAME}
SHARED
show_result.cpp
)

# 添加包含
target_include_directories(${PROJECT_NAME}
PUBLIC
${PROCESS_DIR}
${EASYX_DIR}/include
)

# 链接库
target_link_libraries(${PROJECT_NAME}
PUBLIC
process
${EASYX_DIR}/lib64/libeasyx.a
)
```

## 源码

### pnc_map.h

```c++
#pragma once

class PNC_Map
{
public:
    void mapInfo();
};
```

### pnc_map.cpp

```c++
#include <iostream>
#include "pnc_map.h"

void PNC_Map::mapInfo()
{
    std::cout << "this is pnc_map" << std::endl;
}
```

### process.h

```c++
#pragma once
#include "pnc_map.h"

class Process
{
public:
    void planProcess();
public:
    PNC_Map my_map;
};
```

### process.cpp

```c++
#include <iostream>
#include "process.h"

void Process::planProcess()
{
    std::cout << "this is planning process" << std::endl;
    my_map.mapInfo();
    
    Eigen::MatrixXd m(2,2);
    m(0, 0) = 3;
    m(1, 0) = 5;
    m(0, 1) = -1;
    m(1, 1) = m(1, 0) + m(0, 1);
    std::cout << m << std::endl;
    
    std::cout << "planning success!" << std::endl;
}
```

### show_result.h

```c++
#pragma once

class ShowResult
{
public:
    void drawResult();
};
```

### show_result.cpp

```c++
#include <iostream>
#include "show_result.h"
#include <graphics.h>

void ShowResult::drawResult()
{
    initgraph(1000, 1000);
    setbkcolor(WHITE);
    cleardevice();

    std::cout << "draw circles: " << std::endl;

    setlinecolor(BLACK);
    setlinestyle(PS_SOLID, 4);
    circle(500, 500, 200);
    circle(500, 500, 400);

    system("pause");
    closegraph();
}
```

### planning_main.cpp

```c++
#include <iostream>
#include "process.h"
#include "show_result.h"

int main()
{
    std::cout << "planning start" << std::endl;
    Process pro;
    pro.planProcess();
    std::cout << "planing end" << std::endl;

    std::cout << "show result: " << std::endl;

    ShowResult show;
    show.drawResult();

    return 0;
}
```

