## 项目结构

```powershell
cmake_planning_test/
│  planning_main.cpp
│ 
├─bin
│      libcommon.dll
│      planning_main.exe
│
├─build
├─include
│      pnc_map.h
│      process.h
│
└─src
	   pnc_map.cpp
	   process.cpp
```

## CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.29.0)
project(planning)
set(CMAKE_CXX_STANDARD 17)

# Windows 平台下将动态库和exe都指定到bin目录下
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)
# CMAKE_LIBRARY_OUTPUT_DIRECTORY Linux 平台下指定动态库的位置
# CMAKE_ARCHIVE_OUTPUT_DIRECTORY Linux 平台下指定静态库的位置

# 查找路径下的所有源文件并存放到变量 SRC_LISTS 中
aux_source_directory(${PROJECT_SOURCE_DIR}/src SRC_LISTS)
# 生成动态库
add_library(common
SHARED
${SRC_LISTS}
)

# 为目标(动态库)添加包含目录
target_include_directories(common
PUBLIC
${PROJECT_SOURCE_DIR}/include
)

# 生成可执行文件
add_executable(planning_main planning_main.cpp)

# 为目标(可执行文件)添加包含目录
target_include_directories(planning_main
PUBLIC
${PROJECT_SOURCE_DIR}/include
)

# 为目标(可执行文件)链接依赖库
target_link_libraries(planning_main
PUBLIC
common
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
    std::cout << "planning success!" << std::endl;
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

