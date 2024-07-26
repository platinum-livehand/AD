## 问题背景

![](..\picture\VSCode远程-1.png)

​	由于是在 Windows 下的 VSCode 连接远程 Unbuntu 虚拟机，VSCode 插件中的执行 cmake 的路径为本机的路径 `C:\cmake\bin\cmake` 需要额外设置 `settings.json` 文件确保可以正确找到 cmake 执行程序。

## 通过 观察 Ubuntu 下 VSCode 使用 cmake 执行的命令

![](..\picture\VSCode远程-2.png)

## 使用 ChatGPT 生成 settings.json 文件如下

![](..\picture\VSCode远程-3.png)

```json
{
  "cmake.cmakePath": "/usr/bin/cmake",
  "cmake.generator": "Unix Makefiles",
  "cmake.configureOnOpen": true,
  "cmake.buildDirectory": "${workspaceFolder}/build",
  "cmake.sourceDirectory": "${workspaceFolder}",
  "cmake.defaultBuildType": "Debug",
  "cmake.loggingLevel": "DEBUG",
  "cmake.configureArgs": [
    "-DCMAKE_EXPORT_COMPILE_COMMANDS=TRUE",
    "-DCMAKE_C_COMPILER=/usr/bin/gcc",
    "-DCMAKE_CXX_COMPILER=/usr/bin/g++"
  ],
  "cmake.buildArgs": [],
  "cmake.buildToolArgs": []
}
```

## 创建.vscode 文件夹和setting.json文件

![](..\picture\VSCode远程-5.png)

## 成功编译

![](..\picture\VSCode远程-4.png)

## 测试程序

![](..\picture\VSCode远程-6.png)