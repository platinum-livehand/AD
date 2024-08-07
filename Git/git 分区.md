## Git 分区

​	在 Git 中，有三个主要的区域，工作区(Working Directory)、暂存区(Staging Area)和本地仓库(Local Repository)。

## 工作区(Working Directory)

- 工作区时你正在开发的地方。这里存放的是你当前正在编辑的项目文件。
- 任何文件的修改、新增或删除操作都是在工作区进行的。

## 暂存区(Stgaing Area)

- 暂存区是一个临时区域，用来保存将被提交到版本库的变更。
- 使用 `git add` 命令可以将文件从工作区添加到暂存区。
- 暂存区中的内容是准备提交的文件和变更，它们将包含在下次提交中。

## 本地仓库(Local Repository)

- 本地仓库是存放项目完整历史的地方。每次提交(commit)都会将暂存区中的内容保存到本地仓库。
- 使用 `git commit`命令可以将暂存区中的内容提交到本地仓库。
- 本地仓库保留了项目的所有提交历史，可以方便地进行版本回溯和产看变更记录。

## 工作流示意图

```
工作区 (Working Directory)
      │
      │ (git add)
      ▼
暂存区 (Staging Area)
      │
      │ (git commit)
      ▼
本地仓库 (Local Repository)
      │
      │ (git push)
      ▼
远程仓库 (Remote Repository)
```

![](..\picture\git分区.png)