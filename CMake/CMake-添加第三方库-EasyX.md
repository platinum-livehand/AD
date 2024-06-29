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
│          CMakeLists.txt
│          show_result.cpp
│          show_result.h
│
├─test
└─third_party
    └─EasyX
        │  readme.txt
        │
        ├─include
        │      easyx.h
        │      graphics.h
        │
        ├─lib-for-devcpp_5.4.0
        │      libeasyx.a
        │
        ├─lib32
        │      libeasyx.a
        │
        └─lib64
                libeasyx.a
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

set(CMAKE_CXX_STANDARD 17)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)

# 目录变量
set(PROCESS_DIR ${CMAKE_SOURCE_DIR}/src/process)
set(PNC_MAP_DIR ${CMAKE_SOURCE_DIR}/src/pnc_map)
set(SHOW_RESULT_DIR ${CMAKE_SOURCE_DIR}/src/show_result)
set(EasyX_DIR ${CMAKE_SOURCE_DIR}/third_party/EasyX)

# 添加子目录
add_subdirectory(src)
```

### src 目录

```cmake
project(planning_main)

add_subdirectory(pnc_map)
add_subdirectory(process)
add_subdirectory(show_result)

add_executable(${PROJECT_NAME} planning_main.cpp)

# process 动态库
target_include_directories(${PROJECT_NAME}
PUBLIC
${PROCESS_DIR}
)

target_link_libraries(${PROJECT_NAME}
PUBLIC
process
)

# show_result 动态库
target_include_directories(${PROJECT_NAME}
PUBLIC
${SHOW_RESULT_DIR}
)

target_link_libraries(${PROJECT_NAME}
PUBLIC
show_result
)
```

### pnc_map 目录

```cmake
project(pnc_map)

add_library(${PROJECT_NAME}
SHARED
pnc_map.cpp
)
```

### process 目录

```cmake
project(process)

add_library(${PROJECT_NAME}
SHARED
process.cpp
)

target_include_directories(${PROJECT_NAME}
PUBLIC
${PNC_MAP_DIR}
)

target_link_libraries(${PROJECT_NAME}
PUBLIC
pnc_map
)
```

### show_result 目录

```cmake
project(show_result)

add_library(${PROJECT_NAME}
SHARED
show_result.cpp
)

# 添加 process 库
target_include_directories(${PROJECT_NAME}
PUBLIC
${PROCESS_DIR}
)

target_link_libraries(${PROJECT_NAME}
PUBLIC
process
)

# 添加 EasyX 库
target_include_directories(${PROJECT_NAME}
PUBLIC
${EasyX_DIR}/include
)

target_link_libraries(${PROJECT_NAME}
PUBLIC
${EasyX_DIR}/lib64/libeasyx.a
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

