## 常用命令

```shell
vcpkg search [pat]	搜索可安装的包
vcpkg install <pkg>...	安装包
vcpkg remove <pkg>...	卸载包
vcpkg remove --outdated	卸载所有过期包
vcpkg list	列出已安装的包
vcpkg update	显示用于更新的包列表
vcpkg upgrade	重新生成所有过期包
vcpkg hash <file> [alg]	通过特定算法对文件执行哈希操作，默认为 SHA512
vcpkg integrate install	使已安装包在用户范围内可用。 首次使用时需要管理权限
vcpkg integrate remove	删除用户范围的集成
vcpkg integrate project	为使用单个 VS 项目生成引用 NuGet 包
vcpkg export <pkg>... [opt]...	导出包
vcpkg edit <pkg>	打开端口进行编辑（使用 %EDITOR%，默认为“code”）
vcpkg create <pkg> <url> [archivename]	创建新程序包
vcpkg cache	列出缓存的已编译包
vcpkg version	显示版本信息
vcpkg contact --survey	显示联系信息，以便发送反馈。
```

