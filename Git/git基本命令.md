## Git 命令

![](D:\C++Project\C++\AD\picture\git-4.png)

## 初始化 Git 仓库

​	首先 `mkdir` 创建一个空目录

![](..\picture\git-1.png)

​	主目录中就会出现一个空目录，接下来使用 `git init` 把目录变成 Git 可管理的仓库。

![](..\picture\git-2.png)

​	在 `learngit` 目录下创建一个 `readme.txt` 文档。

![](..\picture\git-3.png)

## 添加文件到 Git 仓库

- `git add .`命令将文件添加到**版本控制**中。
- `git commit -m "xxxxx"`命令将更改提交到**本地仓库**中。

![](..\picture\git-5.png)

## 提交并查看修改

​	如果我们修改了 `readme.txt` 文档中的内容，想让知道仓库的状态，使用 `git status`命令查看结果。

![](..\picture\git-6.png)

​	以上输出告诉我们 `readme.txt` 被修改过了，但没有提交。如果想知道具体修改了什么内容，则输入 `git diff`：

![](..\picture\git-7.png)

提交修改 `git add .` +`git commit -m "xxxxx"`

![](..\picture\git-8.png)

![](..\picture\git-9.png)

## 查看提交历史

`git log` 查看提交历史，`git log --oneline`简洁历史。

![](..\picture\git-10.png)

## 回退版本

​	在 Git 中，`HEAD`表示当前版本，也就是最新的提交。而上个版本就是 `HEAD^`，上上个版本就是 `HEAD^^`，x个版本可以写为 `HEAD~x`。

​	想让 `readme.txt` 回退到上个版本，使用 `git reset --hard HEAD^`。

![](..\picture\git-11.png)

​	再使用 `git log` 查看记录，第三次提交被撤回。

![](..\picture\git-12.png)

​	如果又需要把第三次提交退回使用 `git reflog`他会记录你的每一次命令。

![](..\picture\git-13.png)

​	再使用  `git reset --hard 66af877`既可以恢复第三次提交。

![](..\picture\git-14.png)
