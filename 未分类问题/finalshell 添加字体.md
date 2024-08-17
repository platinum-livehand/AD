## 问题背景

​	在给 vim 安装 vimplus 插件的时候，出现乱码需要调整字体为 `Droid Sans Mono Nerd Font`，从 github 上[下载](https://github.com/ryanoasis/nerd-fonts/releases/tag/v3.2.1)后安装在 windows 上，但是 `FinalShell` 不能像 `XShell` 一样自动检测系统字体（或许要重新安装？因为我下载 XShell 是在安装字体之后）。

## 解决方法

- 在 finalshell 安装目录 `finalshell\jre\lib` 下创建 `fonts` 文件夹，把在 github 上下载的字体文件解压后（`.ttf` 或者 `.otf`文件）放在该目录下。

  ![](..\picture\finalshell-1.png)

![](..\picture\finalshell-2.png)

- 打开 finalshell 查看字体。
- ![](..\picture\finalshell-3.png)