## tar

​	在 Linux 操作系统中默认自带两个原始的压缩工具分别是 `gzip` 和 `bzip2`，但是它们都有先天的缺陷，**不能打包压缩文件，每个文件都会生成一个单独的压缩包**，并且**压缩之后不会保留原文件**。而 **Linux 中自带一个打包工具 `tar`** ，默认情况下该工具是不能进行压缩操作的，在这种情况下，`tar` 和 `gzip` ，`bzip2` 就可以取长补短。

## 压缩（*.tar.gz / *.tar.bz2）

​	如果使用 tar 完成文件压缩，涉及的参数如下，在使用过程中参数没有先后顺序。

- `c`：创建压缩文件。
- `z`：使用 gzip 的方式进行文件压缩。
- `j`：使用 bzip2 的方式进行文件压缩。
- `v`：压缩过程中显示压缩信息，可以省略不写。
- `f`：指定压缩包的名字。

```shell
# 语法: 
$ tar 参数 生成的压缩包的名字 要压缩的文件(文件或者目录)

# 备注: 关于生成的压缩包的名字, 建议使用标准后缀, 方便识别:
	- 压缩使用 gzip 方式,  标准后缀格式为: .tar.gz
	- 压缩使用 bzip2 方式, 标准后缀格式为: .tar.bz2	
```

### 使用 `gzip` 的方式进行文件压缩。

```shell
# 查看目录内容
qy@ubuntu:~/linux_command_train$ ll
总用量 92
drwxrwxr-x  4 qy qy  4096 8月  15 21:53 ./
drwxr-xr-x 24 qy qy  4096 8月  15 21:51 ../
-rw-rw-r--  1 qy qy 10327 8月  15 21:42 all.zip
drwxrwxr-x  2 qy qy  4096 8月  15 16:07 bin/
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
-rw-rw-r--  1 qy qy  4851 8月  15 21:39 part.tar.bz2
drwxrwxr-x  2 qy qy  4096 8月  15 21:50 temp/
-rwxrwxr-x  1 qy qy 17096 8月  15 21:53 testgcc*
-rw-rw-r--  1 qy qy  1820 8月  15 21:53 testgcc.c
-rw-rw-r--  1 qy qy  2982 8月  15 21:32 testTag.txt
-rw-rw-r--  1 qy qy    12 8月  15 17:29 test.txt
# 压缩目录中所有文件，如果要压缩某几个文件，直接指定文件名即可
qy@ubuntu:~/linux_command_train$ tar zcvf all.tar.gz *
all.zip
bin/
myprogram
myprogram.c
part.tar.bz2
temp/
testgcc
testgcc.c
testTag.txt
test.txt

# 查看目录文件，多了一个压缩文件 all.tar.gz
qy@ubuntu:~/linux_command_train$ ll
总用量 112
drwxrwxr-x  4 qy qy  4096 8月  15 21:56 ./
drwxr-xr-x 24 qy qy  4096 8月  15 21:51 ../
-rw-rw-r--  1 qy qy 17302 8月  15 21:56 all.tar.gz # 压缩文件
-rw-rw-r--  1 qy qy 10327 8月  15 21:42 all.zip
drwxrwxr-x  2 qy qy  4096 8月  15 16:07 bin/
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
-rw-rw-r--  1 qy qy  4851 8月  15 21:39 part.tar.bz2
drwxrwxr-x  2 qy qy  4096 8月  15 21:50 temp/
-rwxrwxr-x  1 qy qy 17096 8月  15 21:53 testgcc*
-rw-rw-r--  1 qy qy  1820 8月  15 21:53 testgcc.c
-rw-rw-r--  1 qy qy  2982 8月  15 21:32 testTag.txt
-rw-rw-r--  1 qy qy    12 8月  15 17:29 test.txt
```

### 使用`bzip2`的方式进行文件压缩

```shell
qy@ubuntu:~/linux_command_train$ ll
总用量 92
drwxrwxr-x  4 qy qy  4096 8月  15 22:08 ./
drwxr-xr-x 24 qy qy  4096 8月  15 21:51 ../
-rw-rw-r--  1 qy qy 17302 8月  15 21:56 all.tar.gz
drwxrwxr-x  2 qy qy  4096 8月  15 16:07 bin/
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
drwxrwxr-x  2 qy qy  4096 8月  15 21:50 temp/
-rwxrwxr-x  1 qy qy 17096 8月  15 21:53 testgcc*
-rw-rw-r--  1 qy qy  1820 8月  15 21:53 testgcc.c
-rw-rw-r--  1 qy qy  2982 8月  15 21:32 testTag.txt
-rw-rw-r--  1 qy qy    12 8月  15 17:29 test.txt
qy@ubuntu:~/linux_command_train$ tar jcvf part.tar.bz2 testgcc.c
testgcc.c
qy@ubuntu:~/linux_command_train$ ll
总用量 96
drwxrwxr-x  4 qy qy  4096 8月  15 22:09 ./
drwxr-xr-x 24 qy qy  4096 8月  15 21:51 ../
-rw-rw-r--  1 qy qy 17302 8月  15 21:56 all.tar.gz
drwxrwxr-x  2 qy qy  4096 8月  15 16:07 bin/
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
-rw-rw-r--  1 qy qy   713 8月  15 22:09 part.tar.bz2
drwxrwxr-x  2 qy qy  4096 8月  15 21:50 temp/
-rwxrwxr-x  1 qy qy 17096 8月  15 21:53 testgcc*
-rw-rw-r--  1 qy qy  1820 8月  15 21:53 testgcc.c
-rw-rw-r--  1 qy qy  2982 8月  15 21:32 testTag.txt
-rw-rw-r--  1 qy qy    12 8月  15 17:29 test.txt
```

## 解压缩（*.tar.gz / *.tar.bz2）

​	如果使用 tar 进行文件的解压缩，涉及的参数如下，在使用中参数没有先后顺序。

- `x`：释放压缩文件内容。
- `z`：使用 `gzip` 的方式进行文件压缩，压缩包后缀为 `.tar.gz`。
- `j`：使用 `bzip` 的方式进行文件压缩，压缩包后缀为 `.tar.bz2`。
- `v`：解压缩过程中显示解压缩信息。
- `f`：指定压缩包的名字。

​	使用以上参数是将压缩包解压到当前目录，如果需要解压到指定目录，需要指定参数 `-C`。

```shell
# 语法1: 解压到当前目录中
$ tar 参数 压缩包名 

# 语法2: 解压到指定目录中
$ tar 参数 压缩包名 -C 解压目录
```

### 使用 gzip 的方式进行文件解压缩

```shell
# 查看目录文件信息
qy@ubuntu:~/linux_command_train$ ll
总用量 92
drwxrwxr-x  3 qy qy  4096 8月  15 22:16 ./
drwxr-xr-x 24 qy qy  4096 8月  15 21:51 ../
-rw-rw-r--  1 qy qy 17302 8月  15 21:56 all.tar.gz
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
-rw-rw-r--  1 qy qy   713 8月  15 22:09 part.tar.bz2
drwxrwxr-x  2 qy qy  4096 8月  15 21:50 temp/
-rwxrwxr-x  1 qy qy 17096 8月  15 21:53 testgcc*
-rw-rw-r--  1 qy qy  1820 8月  15 21:53 testgcc.c
-rw-rw-r--  1 qy qy  2982 8月  15 21:32 testTag.txt
-rw-rw-r--  1 qy qy    12 8月  15 17:29 test.txt

# 将 all.tar.gz 压缩包压缩到 temp 目录中
qy@ubuntu:~/linux_command_train$ tar zxvf all.tar.gz -C temp
all.zip
bin/
myprogram
myprogram.c
part.tar.bz2
temp/
testgcc
testgcc.c
testTag.txt
test.txt

# 查看 temp 目录内容，都是从压缩包中释放出来的
qy@ubuntu:~/linux_command_train$ ll temp/
总用量 92
drwxrwxr-x 4 qy qy  4096 8月  15 22:17 ./
drwxrwxr-x 3 qy qy  4096 8月  15 22:16 ../
-rw-rw-r-- 1 qy qy 10327 8月  15 21:42 all.zip
drwxrwxr-x 2 qy qy  4096 8月  15 16:07 bin/
-rwxrwxr-x 1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r-- 1 qy qy   236 8月  15 15:14 myprogram.c
-rw-rw-r-- 1 qy qy  4851 8月  15 21:39 part.tar.bz2
drwxrwxr-x 2 qy qy  4096 8月  15 21:50 temp/
-rwxrwxr-x 1 qy qy 17096 8月  15 21:53 testgcc*
-rw-rw-r-- 1 qy qy  1820 8月  15 21:53 testgcc.c
-rw-rw-r-- 1 qy qy  2982 8月  15 21:32 testTag.txt
-rw-rw-r-- 1 qy qy    12 8月  15 17:29 test.txt
```

### 使用 bzip2 的方式进行文件解压缩

```shell
# 删除 temp 目录中所有的文件
qy@ubuntu:~/linux_command_train$ rm temp/* -rf

# 查看 temp 目录中的文件信息
qy@ubuntu:~/linux_command_train$ ll temp/
总用量 8
drwxrwxr-x 2 qy qy 4096 8月  15 22:22 ./
drwxrwxr-x 3 qy qy 4096 8月  15 22:16 ../

# 将 part.tar.bz2 中的文件压缩到 temp 目录中
qy@ubuntu:~/linux_command_train$ tar jxvf part.tar.bz2 -C temp
testgcc.c
qy@ubuntu:~/linux_command_train$ ll temp/
总用量 12
drwxrwxr-x 2 qy qy 4096 8月  15 22:22 ./
drwxrwxr-x 3 qy qy 4096 8月  15 22:16 ../
-rw-rw-r-- 1 qy qy 1820 8月  15 21:53 testgcc.c
```

