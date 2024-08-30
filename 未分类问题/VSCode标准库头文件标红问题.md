## 问题现象

​	使用 vscode 编写 C++ 代码时，包含标准库头文件标红。

![](..\picture\vscode 问题-1.jpg)

​	程序可以正常编译和运行。

### 编译结果

![](..\picture\vscode 问题-2.png)

### 运行结果

![](..\picture\vscode 问题-3.png)

## 分析问题出现的原因

​	从开始在 Windows 上使用 VSCode 编写 C++ 代码时，这个问题一直没有解决，直到看到一篇博客提出了[**相同的问题**](https://ask.csdn.net/questions/7652063)。结合使用 VSCode 远程连接服务器编写代码时遇到的问题，找到了原因：

- CMake 的设置只是指定了编译器的位置(`gcc.exe`)，但是 VSCode 本质上只是一个**记事本**并不是一个 **IDE** ，在编写代码的时候依赖于 C/C++ 插件来做一些提示。即文件的编译能通过是因为它被 CMake 指定了编译器路径，但是提示并不属于编译时，而是属于代码编写阶段，需要告知 C/C++ 插件编译器依赖的路径，能正确显示。

​	这个问题的本质与我之前遇到的——**[使用VSCode远程连接Ubuntu没有代码提示的问题](D:\C++Project\C++\AD\Linux\使用VSCode远程连接Ubuntu没有代码提示的问题.md)**——原理类似，需要指定编译器依赖的路径。

## 问题解决方案

​	由于在之前的问题中使用的是添加 `settings.json` 解决没有代码提示的问题。这次使用另外一种，即添加 `c_cpp_properties.json`。

![](..\picture\vscode 问题-4.png)

### c_cpp_properties.json

```json
{
    "configurations": [
        {
            "name": "Default",            
            "compileCommands": "${workspaceFolder}/build/compile_commands.json",
            "intelliSenseMode": "windows-gcc-x64"            
        }
    ],
    "version": 4
}
```

### CMakeLists.txt 中增加

```cmake
set(CMAKE_EXPORT_COMPILE_COMMANDS TRUE)
```

​	作用是在生成目录，产生一个 `compile_commands.json` 文件，并且通过 `compile_commands.json` 找头文件，不按指定目录来找 。`"compileCommands": "${workspaceFolder}/build/compile_commands.json"`需要改成你实际生成的相对路径(一般为`build`目录)。

​	**添加完毕后需要清空 `build` 文件夹以及`bin`和`lib`文件夹(如果有的话)，重新生成，不然会生成失败。**

![](..\picture\vscode 问题-5.png)