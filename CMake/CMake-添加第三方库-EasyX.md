## 添加第三方库-EasyX

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

