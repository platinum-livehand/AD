## 项目结构

```powershell
cmake_planning_test/
│  CMakeLists.txt
│  planning_main.cpp
│  pnc_map.cpp
│  pnc_map.h
│  process.cpp
│  process.h
│
├─bin
│      planning_main.exe
│
└─build
```

## CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.29.0)
project(planning)
set(CMAKE_CXX_STANDARD 17)

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
aux_source_directory(. SRC_LISTS)
add_executable(planning_main ${SRC_LISTS})
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

