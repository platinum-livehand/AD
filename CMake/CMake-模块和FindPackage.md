​	在 CMake 中，**模块(Modules)**和**`find_package`**命令共同构成了一个强大的系统，用于查找、配置和使用外部库或包。

## CMake 模块

​	**模块** 是一系列预定义的 CMake 脚本文件，它们提供了一系列通用功能，如查找库、设置编译选项、定义平台相关的构建规则等。这些脚本文件通常位于 CMake 安装目录的 Modules 子目录下，或者由用户自定义并放置在项目的某个目录中。模块文件的拓展名是 `.cmake`。

​	**模块文件** 的核心作用是封装查找和配置外部依赖的逻辑。当项目需要某个外部库时，可以通过调用 `find_package` 命令，并指定库的名字，CMake 会在 CMAKE_MODULE_PATH 指定的目录中查找与该库相应的模块文件(如 `FindXYZ.cmake`) ，然后执行该模块文件中的逻辑来查找并配置该库。

## FindPackage

​	`find_package` 是 CMake 中的一个关键命令，用于查找并加载外部项目的配置信息。当调用此命令时，CMake 会根据库的名字查找对应的模块文件(默认情况下)，执行该模块文件中的代码来定位库的头文件目录、库文件、以及其他可能需要的配置信息。

该命令的基本形式时：

```cmake
find_package(PackageName [REQUIRED] [COMPONENTS component1 component2 ...]
              [VERSION version] [EXACT] [QUIET]
              [MODULE] [NO_MODULE]
              [CONFIG] [NO_DEFAULT_PATH]
              [PATHS paths...]
              [HINTS hints...]
              [CMAKE_FIND_ROOT_PATH_BOTH | ONLY_CMAKE_FIND_ROOT_PATH | NO_CMAKE_FIND_ROOT_PATH])
```

- PackageName：要查找的包的名称。
- REQUIRED：如果找不到指定的包，CMake 配置阶段将失败。
- COMPONENTS：指定需要的库的组件。
- MODULE：指定使用模块方式查找(默认)。
- NO_MODULE：不使用模块查找，而是使用 CMake 内置的简单查找逻辑。
- CONFIG：优先使用配置文件(如`<PackageName>Configure.cmake`)来查找包。

​	一旦 `find_package` 成功，它通常会设置一些输出变量，如 `<PackageName>_FOUND`，`<PackageName>_INCLUDE_DIRS`，`<PackageName>_LIBRARIES`等，供后续的`add_executable`或 `add_library`命令使用，以正确地链接库和包含头文件路径。

如查找 Boost 库并设置其为必需：

```cmake
find_package(Boost REQUIRED COMPONENTS system filesystem)
```

## 二者的交互

​	当调用 find_package 时，如果指定了一个库名，CMake 会查找与之对应的模块文件(如 `FindOpenCV.cmake`)，执行该模块中的查找逻辑。这个模块文件会根据库的安装情况。设置一系列变量，如 `OpenCV_LIBRARIES`和 `OpenCV_INCLUDE_DIRS`，然后项目中的其他指令(如 `target_link_libraries` 和 `target_include_directories`)就可以使用这些变量，从而确保构建过程中正确地使用该库。

## 添加第三方库 EasyX

### 项目结构：

```powershell
test/
│  CMakeLists.txt
│  main.cpp
│      
├─bin
│      cmake_test.exe
│
├─EasyX
│  │  readme.txt
│  │
│  ├─include
│  │      easyx.h
│  │      graphics.h
│  │
│  ├─lib-for-devcpp_5.4.0
│  │      libeasyx.a
│  │
│  ├─lib32
│  │      libeasyx.a
│  │
│  └─lib64
│          libeasyx.a
│
├─inc
├─lib
└─src
```

### CMakeLists.txt

```cmake
# windows第一次执行 cmake .. -G "MinGW Makefiles"
CMAKE_MINIMUM_REQUIRED (VERSION 3.8)

SET(CMAKE_C_COMPILER "D:/Qt/Qt5.12.6/Tools/mingw730_64/bin/gcc.exe")
SET(CMAKE_CXX_COMPILER "D:/Qt/Qt5.12.6/Tools/mingw730_64/bin/g++.exe")

# 项目名称
PROJECT(cmake_test)

# 设置C++标准
SET(CMAKE_CXX_STANDARD 14)
SET(CMAKE_CXX_STANDARD_REQUIRED ON)
SET(CMAKE_CXX_EXTENSIONS OFF)

# 添加源文件目录
AUX_SOURCE_DIRECTORY(${PROJECT_SOURCE_DIR}/src SRC_LIST)

# 添加头文件目录
INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR}/inc)

# 设置exe输出路径
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

# 设置lib输出路径
SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

# 生成exe    
ADD_EXECUTABLE(${PROJECT_NAME} ${SRC_LIST} main.cpp)

# 添加 EasyX 库
TARGET_LINK_LIBRARIES(${PROJECT_NAME} ${PROJECT_SOURCE_DIR}/EasyX/lib64/libeasyx.a)
TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME} PRIVATE ${PROJECT_SOURCE_DIR}/EasyX/include)
```

### main.cpp

```c++
#include<graphics.h>
#include<conio.h>

int main()
{

    initgraph(666, 666);  // 初始化为 666*666 的画布
    /*    circle    */
    setcolor(BLUE);   //circle 的线条为蓝色
    setfillcolor(RED);  //circle 内红色填充
    setbkcolor(GREEN);
    fillcircle(100, 100, 20); //circle center 为（100，100）半径 20


    getch();     // 按任意键继续
    closegraph();    // 关闭图形界面
    return 0;
}
```