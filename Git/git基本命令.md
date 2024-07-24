## Git 命令

![](D:\C++Project\C++\AD\picture\git-4.png)

## 初始化 Git 仓库

​	首先 `mkdir` 创建一个空目录

![](..\picture\git-1.png)

​	主目录中就会出现一个空目录，接下来使用 `git init` 把目录变成 Git 可管理的仓库。

![](..\picture\git-2.png)

​	在 `Thread_Pool` 目录下创建一个 `.gitignore` 文档。

![](..\picture\git-3.png)

## 添加文件到 Git 仓库

- `git add .`命令将文件添加到**版本控制**中。
- `git commit -m "xxxxx"`命令将更改提交到**本地仓库**中。

![](..\picture\git-5.png)

## 提交并查看修改

​	如果我们修改了文档中的内容，想让知道仓库的状态，使用 `git status`命令查看结果。

![](..\picture\git-6.png)

​	以上输出告诉我们被修改过了，但没有提交。如果想知道具体修改了什么内容，则输入 `git diff`

## 查看提交历史

`git log` 查看提交历史，`git log --oneline`简洁历史。

![](..\picture\git-10.png)

## 提交至远程仓库

​	在 `github` 上新建一个仓库，复制仓库地址，然后使用命令`git remote add origin xxx`将本地仓库关联到 `github` 上。

![](..\picture\git-11.png)

![](..\picture\git-12.png)

​	由于 github 不再支持使用账户密码进行身份验证，所以需要使用指令 `git remote set-url origin https://<your_token>@github.com/<USERNAME>/<REPO>.git` 添加Token令牌。

![](D:\C++Project\C++\AD\picture\git-13.png)

​	使用命令 `git push -u origin master` 推送至远程仓库

![](..\picture\git-14.png)
