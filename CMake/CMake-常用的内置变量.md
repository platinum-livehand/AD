## CMake

​	CMake 提供了许多内置变量，用于配置和控制构建过程。这些变量可以分为几类，包括路径和目录、项目和目标、编译器和平台、构建类型、以及用户定义变量。

## 路径和目录变量

- **`CMAKE_SOURCE_DIR`：**项目顶层目录(CMakeLists.txt 所在的目录)。

​	如果你有多个级别的 CMakeLists.txt 文件，那么每个文件都应该有一个相对路径，指向其所在目录的父级目录。

假设你有如下的文件结构：

```powershell
project/  
│  		CMakeLists.txt
│
├─src  
│       CMakeLists.txt  
│ 
└─include  
      	CMakeLists.txt
```

​	在 `src/CMakeLists.txt` 中，`CMAKE_SOURCE_DIR` 将指向 `/project` 目录。而在 `include/CMakeLists.txt` 中，`CMAKE_SOURCE_DIR` 也将指向 `/project` 目录。因为这两个子目录都是 `/project`目录下的子目录。

- **`CMAKE_BINARY_DIR`：**构建目录的顶层目录(运行 CMake 命令的目录 `build/`)。
- **`CMAKE_CURRENT_SOURCE_DIR`：**当前正处理的源码路径 ，当前 CMakeLists.txt 所在的路径。
- **`CMAKE_CURRENT_BINARY_DIR`**：当前处理的 CMakeLists.txt 文件的二进制目录。

​	由 `add_subdirectory`添加的目录将会在**构建树(Build Tree)**中创建一个**构建目录**。理论上，每个构建目录都有自己的输出目录。

## 项目和目标变量

- **`PROJECT_NAME`：**项目名称(由 `project()` 命令指定)。
- **`PROJECT_SOURCE_DIR`：**工程根目录。
- **`PROJECT_BINARY_DIR`：**运行cmake的目录，通常是`${PROJECT_SOURCE_DIR}/build`
- **`CMAKE_PROJECT_NAME`：**最顶层项目的名称。
- **`CMAKE_CURRENT_LIST_DIR`**：当前 CMakeLists.txt 文件所在的目录。
- **`CMAKE_CURRENT_LIST_FILE`：**当前 CMakeLists.txt 文件的完整路径。

## 编译器和平台变量

- **`CMAKE_C_COMPILER`：**C 编译器的名称和路径。
- **`CMAKE_CXX_COMPILTER`：**C++ 编译器的名称和路径。
- **`CMAKE_C_FLAGS`：**C 编译器的标志。
- **`CMAKE_CXX_FLAGS`：**C++ 编译器的标志。
- **`CMAKE_SYSTEM_NAME`：**系统名称。
- **`CMAKE_SYSTEM_VERSION`：**系统版本。
- **`CMAKE_SYSTEM_PROCESS`：**处理器名称。

## 构建类型变量

- **`CMAKE_BUILD_TYPE`：**构建类型(如 Debug、Release)。

## 其他常用变量

- **`CMAKE_CXX_STANDARD`：**C++ 标准(如 11、14、17)。
- **`CMAKE_RUNTIME_OUTPUT_DIRECTORY`：**可执行文件的输出目录。
- **`CMAKE_LIBRARY_OUTPUT_DIRECTORY`：**库文件的输出目录。
- **`CMAKE_ARCHIVE_OUTPUT_DIRECTORY`：**归档文件的输出目录(如 静态库等)。

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

# 输出一些路径信息
message("Source dir: ${CMAKE_SOURCE_DIR}")
message("Binary dir: ${CMAKE_BINARY_DIR}")
message("Current source dir: ${CMAKE_CURRENT_SOURCE_DIR}")
message("Current binary dir: ${CMAKE_CURRENT_BINARY_DIR}")

# 设置编译器和编译标志
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra")

# 设置输出目录
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# 添加一个可执行文件
add_executable(MyExecutable src/main.cpp)

# 安装目标
install(TARGETS MyExecutable DESTINATION bin)
```

