## 文件详细介绍

```cmd
# ls -l
总计 3405916
lrwxrwxrwx   1 root root          7  4月 22 21:08 bin -> usr/bin
drwxr-xr-x   2 root root       4096  2月 26 20:58 bin.usr-is-merged
drwxr-xr-x   4 root root       4096  7月 10 15:36 boot
dr-xr-xr-x   2 root root       4096  4月 24 19:28 cdrom
drwxr-xr-x  19 root root       4320  7月 17 22:33 dev
drwxr-xr-x 141 root root      12288  7月 12 11:32 etc
drwxr-xr-x   3 root root       4096  7月 10 15:48 home
lrwxrwxrwx   1 root root          7  4月 22 21:08 lib -> usr/lib
lrwxrwxrwx   1 root root          9  4月 22 21:08 lib64 -> usr/lib64
drwxr-xr-x   2 root root       4096  4月  8 22:37 lib.usr-is-merged
drwx------   2 root root      16384  7月 10 14:57 lost+found
drwxr-xr-x   3 root root       4096  7月 10 15:49 media
drwxr-xr-x   2 root root       4096  4月 24 18:47 mnt
drwxr-xr-x   2 root root       4096  4月 24 18:47 opt
dr-xr-xr-x 380 root root          0  7月 17 22:33 proc
drwx------   7 root root       4096  7月 11 17:02 root
drwxr-xr-x  39 root root        980  7月 17 23:07 run
lrwxrwxrwx   1 root root          8  4月 22 21:08 sbin -> usr/sbin
drwxr-xr-x   2 root root       4096  3月 31 17:00 sbin.usr-is-merged
drwxr-xr-x  20 root root       4096  7月 11 10:13 snap
drwxr-xr-x   2 root root       4096  4月 24 18:47 srv
-rw-------   1 root root 3487563776  7月 10 15:13 swap.img
dr-xr-xr-x  13 root root          0  7月 17 22:33 sys
drwxrwxrwt  20 root root       4096  7月 17 23:08 tmp
drwxr-xr-x  12 root root       4096  4月 24 18:47 usr
drwxr-xr-x  14 root root       4096  7月 10 15:47 var

# 文件详细信息介绍
 d      rwx       rwx     r-x     5    robin    robin    4096    Jan 13 17:35    udp
 |       |         |       |      |      |        |       |          |	          |
文件    文件所    文件所   其他人 硬链接  文件     文件   文件大小  文件修改时间     文件名
类型   有者权限  属组权限  权限   计数   所有者   所属组   
```

## 文件类型

​	在 Linux 操作系统中，一共有7种文件类型，这7种类型是根据文件属性进行划分的，而不是根据文件后缀划分的。

- `-`：**普通的文件**，在 Linux 终端中**没有执行权限**的为白色，**压缩包**为**红色**，**可执行程序**为**绿色**。
- `d`：**目录(directory)**，在 Linux 终端中为**蓝色字体**，如果目录的所有权限都是开放的，有绿色的背景色。
- `l`：**软连接文件(link)**，相当于 windows 中的快捷方式，在 Linux 终端中为**淡蓝色(青色)**字体。
- `c`：**字符设备(char)**，在 Linux 终端中为**黄色**字体。
- `b`：**块设备(block)**，在 Linux 终端中为**黄色**字体。
- `p`：**管道文件(pipe)**，在 Linux 终端中为**棕黄色**字体。
- `s`：**本地套接字文件(socket)**，在 Linux 终端中为**粉色**字体。

## 用户类型

​	在 Linux 中有三大类用户：**文件所有者**，**文件所属组用户**，**其他人**，我们可以对同一个文件给这三种人设置不同的操作权限，用于限制用户对文件的访问。

- 文件所有者，Linux中所有的文件都有一个所有者，就是文件的主人。
- 文件所属组，文件的主人属于哪个组，这个文件默认也就属于哪个组。用户组中可以有多个用户，这些组中的其他用户和所有者的权限可以是不一样的。
- 其他人，这个用户既不是文件所有者也不是文件所属组中的用户，就称之为其他人，其他人对文件也可以拥有某些权限。

## 文件权限

​	Linux 中不同的用户可以对文件拥有不同的操作权限，权限一共有四种：读权限，写权限，执行权限，无权限。

- 读权限：使用 `r` 表示，即：read。
- 写权限：使用 `w` 表示，即：write。
- 执行权限：使用 `x` 表示，即：executable。
- 没有权限：使用 `-` 表示。

```shell
   -           rwx          rw-          r--  1 robin robin   2218 Dec  2 17:02 app
   |            |            |            |
文件类型      文件所有      文件所属     其他人权限
             者权限        组权限
```

​	从上边的例子可以看出：

- 文件所有者对文件有 **读**、**写**、**执行**权限。
- 文件所属组用户对文件有 **读**、**写**权限，没有执行权限。
- 其他人对文件有 **读**权限，没有写、执行权限。

## 硬链接计数

​	硬链接计数是一个整数，如果这个数为 N，就说明在一个或者多个目录下一共有 N 个文件，但是这 N 个文件并不占多块磁盘空间，它们使用的是同一块，如果通过其中一个文件修改了磁盘数据，那么其他文件中的内容也就变了。每当我们给磁盘文件创建一个硬链接(使用 `ln` 命令)，磁盘上就会出现一个新的文件名，硬链接计数加1，但是这个新文件并不占用任何磁盘空间，文件名还是映射到原来的磁盘地址上。

```cmd
$ touch testLnCounter.txt
$ ll
总用量 8
drwxrwxr-x  2 qy qy 4096 8月  29 10:55 ./
drwxr-xr-x 27 qy qy 4096 8月  29 09:18 ../
-rw-rw-r--  1 qy qy    0 8月  29 10:55 testLnCounter.txt
$ mkdir test
$ ll
总用量 12
drwxrwxr-x  3 qy qy 4096 8月  29 10:55 ./
drwxr-xr-x 27 qy qy 4096 8月  29 09:18 ../
drwxrwxr-x  2 qy qy 4096 8月  29 10:55 test/
-rw-rw-r--  1 qy qy    0 8月  29 10:55 testLnCounter.txt
```

![](..\picture\Linux文件详细信息-1.png)

硬链接计数的存在对于文件系统有多个重要的意义：

1. **管理文件和目录的引用**：
   - 硬链接计数帮助操作系统追踪文件或目录的引用次数。**当这个计数降到0时，文件或目录会真正被删除，释放磁盘空间。**如果有一个或多个硬链接仍然存在，数据将保留。
2. **节省空间**：
   - 硬链接允许多个文件名指向同一个物理数据块，因此可以节省存储空间。例如，多个文件可以只使用一个物理副本，而不需要创建实际的副本。

## 其他属性

- 文件大小：单位是字节。
- 文件日期：显示的是文件的修改日期，只要文件被更新，日期也会随之变化。
- 文件名：文件名称，如果文件类型是软连接会这样显示：`link->/root/file/test`，后边的路径表示快捷方式链接的是哪个磁盘文件。