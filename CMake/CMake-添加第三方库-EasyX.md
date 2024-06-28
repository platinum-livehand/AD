## 项目结构

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

## CMakeLists.txt

```cmake
# windows第一次执行 cmake .. -G "MinGW Makefiles"
cmake_minimum_required(VERSION 3.29.0)

# 项目名称
project(cmake_test)

# 设置C++标准
set(CMAKE_CXX_STANDARD 14)

# 设置输出目录
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)

# 添加源文件目录
aux_source_directory(${PROJECT_SOURCE_DIR}/src SRC_LIST)

# 添加头文件目录
include_directories(${PROJECT_SOURCE_DIR}/inc)

# 生成可执行程序
add_executable(${PROJECT_NAME} ${SRC_LIST} main.cpp)

# 可执行程序包含 EasyX 库的头文件
target_include_directories(${PROJECT_NAME} 
PUBLIC 
${PROJECT_SOURCE_DIR}/EasyX/include)

# 可执行程序链接 EasyX 库
target_link_libraries(${PROJECT_NAME} 
PUBLIC
${PROJECT_SOURCE_DIR}/EasyX/lib64/libeasyx.a)
```

## main.cpp

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

