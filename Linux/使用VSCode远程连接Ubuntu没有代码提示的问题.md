## 问题背景

​	编写代码的时候发现即使包含了 `#include <stdlib.h>` ，在输入 `malloc()` 时没有代码提示。

![](..\picture\VSCode远程-7.png)

## 解决方法

​	当使用 VSCode 远程连接到 Ubuntu 并编写代码时，使用 CMake 需要在 `settings.json` 中做一些配置，以确保 CMake、编译器和 IntelliSense 设置都正确。

### CMake 路径和生成器

```json
"cmake.cmakePath": "/usr/bin/cmake",
"cmake.generator": "Unix Makefiles",  
```

### CMake 配置和构建目录

```json
"cmake.configureOnOpen": true,
"cmake.buildDirectory": "${workspaceFolder}/build",
"cmake.sourceDirectory": "${workspaceFolder}",
```

### CMake 配置参数

```json
"cmake.configureArgs": [
  "-DCMAKE_EXPORT_COMPILE_COMMANDS=TRUE",
  "-DCMAKE_C_COMPILER=/usr/bin/gcc",
  "-DCMAKE_CXX_COMPILER=/usr/bin/g++"
],
```

### CMake 构建工具参数

​	如果需要为构建工具提供额外参数，可以在这里设置。

```json
"cmake.buildArgs": [],
"cmake.buildToolArgs": [],
```

### C++ IntelliSense 设置

​	确保 IntelliSense 使用正确的编译器路径和包含路径。

```json
"C_Cpp.default.compilerPath": "/usr/bin/gcc",
"C_Cpp.default.includePath": [
    "${workspaceFolder}/**",
    "/usr/include",
    "/usr/local/include"
],
"C_Cpp.intelliSenseEngine": "default",  // 可以设置为 "Tag Parser" 或 "Default"
```

### CMake 日志级别

​	控制 CMake 扩展的日志详细程度的设置选项

```json
"cmake.loggingLevel": "info" 
```

![](..\picture\VSCode远程-9.png)

**`none`**: 当你希望完全禁用 CMake 扩展的日志输出时使用。

**`error`**: 如果你只关心构建过程中的错误，可以选择这个级别。

**`warning`**: 对于希望只关注警告和错误，但不需要普通信息的用户，这个级别是合适的。

**`info`**: 当你需要普通信息和警告、错误信息时，使用这个级别。

**`debug`**: 如果你需要全面的调试信息来排查问题，这个级别最为详细。

## 完整的 settings.json

```json
{
  // CMake 路径和生成器
  "cmake.cmakePath": "/usr/bin/cmake",
  "cmake.generator": "Unix Makefiles",

  // CMake 配置和构建目录
  "cmake.configureOnOpen": true,
  "cmake.buildDirectory": "${workspaceFolder}/build",
  "cmake.sourceDirectory": "${workspaceFolder}",

  // CMake 日志级别
  "cmake.loggingLevel": "info",

  // CMake 配置参数
  "cmake.configureArgs": [
    "-DCMAKE_EXPORT_COMPILE_COMMANDS=TRUE",
    "-DCMAKE_C_COMPILER=/usr/bin/gcc",
    "-DCMAKE_CXX_COMPILER=/usr/bin/g++"
  ],

  // CMake 构建参数
  "cmake.buildArgs": [],
  "cmake.buildToolArgs": [],

  // C++ IntelliSense 设置
  "C_Cpp.intelliSenseEngine": "default",
  "C_Cpp.default.compilerPath": "/usr/bin/gcc",
  "C_Cpp.default.includePath": [
      "${workspaceFolder}/**",
      "/usr/include",
      "/usr/local/include"
  ]
}
```

## 问题解决

![](..\picture\VSCode远程-8.png)