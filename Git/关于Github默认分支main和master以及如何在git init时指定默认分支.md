## 问题背景

​	在使用 git 上传本地项目到 `github` 远程仓库时遇到了一个问题，初始化本地仓库后本地的默认仓库会变为 `master` ，然而在 `github` 的远程仓库中，我们的默认分支为 `main` 。

![](..\picture\git_master_main-1.png)

![](..\picture\git_master_main-2.png)

​	这会使得在远程仓库中创建出 `master` 分支，每次从本地推送都是往 `master` 分支推送，而不是往 `main` 分支推送。

![](..\picture\git_master_main-3.png)

## 解决方案

​	修改分支名

### 初始化时修改

```shell
git init -b main
```

### 初始化后修改

```shell
git branch -m master main
```

![](..\picture\git_master_main-4.png)

​	此时直接使用 `git push -u origin main` 会提示**本地仓库版本低于远程仓库**。

![](..\picture\git_master_main-5.png)

​	使用 `git push -u origin main -f` 强制推送。

![](..\picture\git_master_main-6.png)

### 全局修改

```shell
git config --global init.defaultBranch main
```

### Github设置修改

![](..\picture\git_master_main-7.png)