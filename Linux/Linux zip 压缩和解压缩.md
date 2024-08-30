## zip

​	zip 格式的压缩包在 Linux 中也是很常见的，在某些版本中需要安装才能使用。

```shell
$ sudo apt install zip    	# 压缩
$ sudo apt install unzip	# 解压缩
```

## 压缩（*.zip）

​	使用 `zip` 压缩目录需要注意一点，必须要添加参数 `-r`，这样才能将子目录中的文件一并压缩，如果要压缩的文件中没有目录，该参数可以忽略。另外使用 `zip` 压缩文件，会自动生成文件后桌 `.zip`，因此就不需要额外指定了。

```shell
# 语法: 后自动添加压缩包后缀 .zip, 如果要压缩目录, 需要添加参数 r
$ zip [-r]  压缩包名 要压缩的文件
```

```shell
# 查看目录文件信息
$ ll
总用量 92
drwxrwxr-x  3 qy qy  4096 8月  15 22:16 ./
drwxr-xr-x 24 qy qy  4096 8月  15 21:51 ../
-rw-rw-r--  1 qy qy 17302 8月  15 21:56 all.tar.gz
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
-rw-rw-r--  1 qy qy   713 8月  15 22:09 part.tar.bz2
drwxrwxr-x  2 qy qy  4096 8月  15 22:28 temp/
-rwxrwxr-x  1 qy qy 17096 8月  15 21:53 testgcc*
-rw-rw-r--  1 qy qy  1820 8月  15 21:53 testgcc.c
-rw-rw-r--  1 qy qy  2982 8月  15 21:32 testTag.txt
-rw-rw-r--  1 qy qy    12 8月  15 17:29 test.txt

# 压缩 文件 testgcc.c myprogram.c 得到压缩包 src.zip（不需要指定后缀，自动添加）
$ zip src testgcc.c myprogram.c -r
  adding: testgcc.c (deflated 70%)
  adding: myprogram.c (deflated 35%)
  
# 查看目录文件信息，多了一个压缩包文件 src.zip
$ ll
总用量 96
drwxrwxr-x  3 qy qy  4096 8月  15 22:29 ./
drwxr-xr-x 24 qy qy  4096 8月  15 21:51 ../
-rw-rw-r--  1 qy qy 17302 8月  15 21:56 all.tar.gz
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
-rw-rw-r--  1 qy qy   713 8月  15 22:09 part.tar.bz2
-rw-rw-r--  1 qy qy  1009 8月  15 22:29 src.zip # 得到的压缩文件
drwxrwxr-x  2 qy qy  4096 8月  15 22:28 temp/
-rwxrwxr-x  1 qy qy 17096 8月  15 21:53 testgcc*
-rw-rw-r--  1 qy qy  1820 8月  15 21:53 testgcc.c
-rw-rw-r--  1 qy qy  2982 8月  15 21:32 testTag.txt
-rw-rw-r--  1 qy qy    12 8月  15 17:29 test.txt
```

## 解压缩（*.zip）

​	对应 `zip` 格式的文件解压缩，必须使用 `unzip` 命令，和压缩的时候使用的命令是不同的。如果压缩包中的文件要解压到指定目录需要指定参数 `-d`，默认是解压到当前目录中。

```shell
# 删除 temp 目录中的所有文件
$ rm temp/* -rf
$ ll temp/
总用量 8
drwxrwxr-x 2 qy qy 4096 8月  15 22:28 ./
drwxrwxr-x 3 qy qy 4096 8月  15 22:29 ../

# 将 src.zip 解压缩到 temp 目录中
$ unzip src.zip -d temp/
Archive:  src.zip
  inflating: temp/testgcc.c          
  inflating: temp/myprogram.c

# 查看 temp 目录中的文件信息
$ ll temp/
总用量 16
drwxrwxr-x 2 qy qy 4096 8月  15 22:33 ./
drwxrwxr-x 3 qy qy 4096 8月  15 22:29 ../
-rw-rw-r-- 1 qy qy  236 8月  15 15:14 myprogram.c
-rw-rw-r-- 1 qy qy 1820 8月  15 21:53 testgcc.c
```

