## 链接的创建

​	链接分为两种类型：**软连接** 和 **硬链接**。软链接相当于 Windows 中的快捷方式，硬链接，会多出一个新的文件名，并且硬链接计数会加 1。

## 软连接

```shell
# 语法: ln -s 源文件路径 软链接文件的名字(可以带路径)

$ ll
总用量 40
drwxrwxr-x  3 qy qy  4096 8月  15 15:58 ./
drwxr-xr-x 24 qy qy  4096 8月  15 15:14 ../
drwxrwxr-x  2 qy qy  4096 8月  15 15:54 bin/
-rw-rw-r--  1 qy qy  3109 8月  13 17:50 cp_test.txt
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
$ cd bin/
$ ln -s ~/linux_command_train/myprogram testLn
$ ll
总用量 8
drwxrwxr-x 2 qy qy 4096 8月  15 15:59 ./
drwxrwxr-x 3 qy qy 4096 8月  15 15:58 ../
lrwxrwxrwx 1 qy qy   38 8月  15 15:59 testLn -> /home/qy/linux_command_train/myprogram*
# 执行 testLn
$ ./testLn 
Number of arguments: 1
Argument 0: ./testLn
$ mv testLn ../
$ cd ../
$ ll
总用量 40
drwxrwxr-x  3 qy qy  4096 8月  15 15:59 ./
drwxr-xr-x 24 qy qy  4096 8月  15 15:14 ../
drwxrwxr-x  2 qy qy  4096 8月  15 15:59 bin/
-rw-rw-r--  1 qy qy  3109 8月  13 17:50 cp_test.txt
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
lrwxrwxrwx  1 qy qy    38 8月  15 15:59 testLn -> /home/qy/linux_command_train/myprogram*
# 执行移动后的 testLn
$ ./testLn 
Number of arguments: 1
Argument 0: ./testLn
```

​	在创建软链接的时候，命令中的 源文件路径 **建议使用绝对路径**，这样才能保证创建出的软链接文件在任意目录中移动都可以访问到链接的那个源文件。

## 硬链接

```shell
# 语法: ln 源文件 硬链接文件的名字(可以带路径)

$ ll
总用量 40
drwxrwxr-x  3 qy qy  4096 8月  15 15:59 ./
drwxr-xr-x 24 qy qy  4096 8月  15 15:14 ../
drwxrwxr-x  2 qy qy  4096 8月  15 15:59 bin/
-rw-rw-r--  1 qy qy  3109 8月  13 17:50 cp_test.txt
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
lrwxrwxrwx  1 qy qy    38 8月  15 15:59 testLn -> /home/qy/linux_command_train/myprogram*
$ cd ./bin/
$ ln ../myprogram testLnn
$ ll
总用量 28
drwxrwxr-x 2 qy qy  4096 8月  15 16:07 ./
drwxrwxr-x 3 qy qy  4096 8月  15 15:59 ../
-rwxrwxr-x 2 qy qy 17368 8月  10 16:30 testLnn*
$ ./testLnn 
Number of arguments: 1
Argument 0: ./testLnn
$ mv testLnn ../testLnn
$ cd ..
$ ll
总用量 60
drwxrwxr-x  3 qy qy  4096 8月  15 16:07 ./
drwxr-xr-x 24 qy qy  4096 8月  15 15:14 ../
drwxrwxr-x  2 qy qy  4096 8月  15 16:07 bin/
-rw-rw-r--  1 qy qy  3109 8月  13 17:50 cp_test.txt
-rwxrwxr-x  2 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
lrwxrwxrwx  1 qy qy    38 8月  15 15:59 testLn -> /home/qy/linux_command_train/myprogram*
-rwxrwxr-x  2 qy qy 17368 8月  10 16:30 testLnn*
$ ./testLnn
Number of arguments: 1
Argument 0: ./testLnn
```

​	硬链接和软链接不同，它是通过文件名直接找对应的硬盘地址，而不是基于路径，因此 源文件 使用相对路径即可，无需为其指定绝对路径。**目录不允许创建硬链接**。