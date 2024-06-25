​	在CMake中，**"目标"(Targets)**指的是通过`add_executable`或`add_library`名利创建的可执行文件、静态库或动态库。**"依赖"(Dependencies)**是指在构建这些目标时所依赖的其他目标或库。正确管理目标与其依赖关系对于确保项目顺利编译和链接至关重要。

## 添加目标

### 可执行文件：

```cmake
add_executable(MyExecutable main.cpp)
```

### 静态库：

```cmake
add_library(MyLib STATIC src/mylib.cpp)
```

### 动态库：

​	在Windows上为**.dll**文件，在Unix-like系统上为**.so**文件。

```cmake
add_library(MySharedLib SHARED src/mysharedlib.cpp)
```

## 指定依赖

​	使用`target_link_libraries`命令为你的目标指定依赖项。告诉CMake在链接目标时需要包含哪些库或其他目标。

### 单一依赖：

```cmake
# MyExecutable 依赖于 MyLib
target_link_libraries(MyExecutable MyLib)
```

### 多个依赖及顺序：

​	顺序有时也很重要，特别是在链接顺序敏感的库时。

```cmake
# MyExecutable 依赖于 MyLib 和 AnotherLib
target_link_libraries(MyExecutable MyLib AnotherLib)
```

### 传递依赖和导入目标：

​	当链接到一个库时，如果该库也定义了自己的依赖项，这些依赖项会自动传递给链接它的目标。

```cmake
add_library(MyLib SHARED src/mylib.cpp)
target_link_libraries(MyLib PUBLIC SomeDependency)

add_executable(MyExecutable main.cpp)
# MyLib 的依赖 SomeDependency 会自动传递给 MyExecutable
target_link_libraries(MyExecutable MyLib) 
```

## 导入外部库/第三方库作为依赖

​	对于外部库，你需要通过`find_package`或其他方法找到库，然后将其作为依赖添加。

```cmake
find_package(Boost REQUIRED COMPONENTS system thread)
target_link_libraries(MyExecutable Boost::system Boost::thread)
```

## 公有、私有依赖

- **PUBLIC：**依赖既是下游目标的公有依赖也是自身的一部分。
- **PRIVATE：**依赖仅在当前目标内部可见，不会传递给链接它的目标。
- **INTERFACE：**仅用于导入目标，表示这个依赖需要由链接到当前目标的其他目标自己解决。

```cmake
cmake_minimum_required(VERSION 3.0)
project(MyProject)

add_library(MyLib STATIC src/mylib.cpp)
# 假设 ZLib 已经通过 find_package 找到
target_link_libraries(MyLib PRIVATE ZLib::ZLib)  

add_executable(MyExecutable main.cpp)
# MyLib 的 PRIVATE 依赖不会传递给 MyExecutable
target_link_libraries(MyExecutable MyLib)  

add_library(AnotherLib SHARED src/anotherlib.cpp)
# Boost::system 会成为 AnotherLib 接口的一部分，传递给链接它的目标
target_link_libraries(AnotherLib PUBLIC Boost::system)  

add_executable(MySecondExecutable second_main.cpp)
# MySecondExecutable 会自动链接到 Boost::system
target_link_libraries(MySecondExecutable AnotherLib)  
```

## 完整的CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)

# 项目名称和版本
project(MyProject VERSION 1.0 LANGUAGES CXX)

# 查找并配置外部库Boost
find_package(Boost REQUIRED COMPONENTS system thread)

# 自定义库MyLib及其私有依赖ZLib
add_library(MyLib STATIC src/mylib.cpp)
target_include_directories(MyLib PRIVATE include)  # 添加私有头文件目录
target_link_libraries(MyLib PRIVATE ZLib::ZLib)     # 假设ZLib已通过find_package找到

# 可执行文件MyExecutable依赖于MyLib
add_executable(MyExecutable src/main.cpp)
target_link_libraries(MyExecutable PRIVATE MyLib)

# 另一个库AnotherLib，具有公共依赖Boost::system
add_library(AnotherLib SHARED src/anotherlib.cpp)
target_include_directories(AnotherLib PUBLIC include)  # 公共头文件目录，供使用者访问
target_link_libraries(AnotherLib PUBLIC Boost::system)  # 将Boost::system作为公共依赖暴露

# 第二个可执行文件，依赖于AnotherLib，间接获得Boost::system
add_executable(MySecondExecutable src/second_main.cpp)
target_link_libraries(MySecondExecutable PRIVATE AnotherLib)

# 设置C/C++编译选项（可根据需要调整）
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 打印一些信息
message(STATUS "MyProject version: ${PROJECT_VERSION}")

# 安装规则（可选）
install(TARGETS MyExecutable MySecondExecutable AnotherLib MyLib
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib/static
        INCLUDES DESTINATION include)
```

