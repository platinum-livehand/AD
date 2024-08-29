## 修改文件所有者

​	默认情况下，文件是哪个用户创建出来的，就属于哪个用户，这个用户属于哪个组，文件就属于哪个组。如果有特殊需求，可以修改文件所有者，对应的操作命令是 `chown`。因为修改文件所有者是跨用户操作，普通用户没用这个权限，需要借助管理员权限才能完成该操作。

​	普通用户借助管理员权限执行某些 `shell` 命令，需要在命令前加关键字 `sudo`，但是普通用户默认是没用使用 sudo 的资格的，需要修改 `/etc/sudoers` 文件。

```shell
# 语法1-只修改所有者: 
$ sudo chown 新的所有者 文件名

# 语法2-同时修改所有者和所属组: 
$ sudo chown 新的所有者:新的组名 文件名

# 查看文件所有者：b.txt 属于 robin 用户
$ ll b.txt 
-rw-rw-rw- 2 robin robin 2929 Apr 14 18:53 b.txt

# 将 b.txt 的所有者修改为 luffy
$ sudo chown luffy b.txt
[sudo] password for robin: 
$ ll b.txt 
-rw-rw-rw- 2 luffy robin 2929 Apr 14 18:53 b.txt

# 修改文件所有者和文件所属组
# 查看文件所有者和所属组
$ ll b.txt 
-rw-rw-rw- 2 luffy robin 2929 Apr 14 18:53 b.txt

# 同时修改文件所有者和文件所属组
$ sudo chown robin:luffy b.txt 
$ ll b.txt 
-rw-rw-rw- 2 robin luffy 2929 Apr 14 18:53 b.txt
```

## 修改文件所属组

```shell
# 只修改文件所属的组, 普通用户没有这个权限, 借助管理员的权限
# 语法: sudo chgrp 新的组 文件名

# 查看文件所属组信息
$ ll b.txt 
-rw-rw-rw- 2 robin luffy 2929 Apr 14 18:53 b.txt

# 修改文件所属的组
$ sudo chgrp robin b.txt 
$ ll b.txt 
-rw-rw-rw- 2 robin robin 2929 Apr 14 18:53 b.txt
```

