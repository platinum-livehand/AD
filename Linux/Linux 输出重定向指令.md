## 重定向命令

​	关于重定向使用最多的就是 **输出重定向**，顾名思义就是修改输出的数据的位置，通过重定向操作我们可以非常方便的进行文件的复制，或者文件内容的追加。输出重定向使用的不是某个关键字而是符号 `>` 或者 `>>`。

- `>`：将文件内容写到指定文件中，如果文件中已有数据，则会使用新数据覆盖原数据。
- `>>`：将输出的内容追加到指定的文件尾部。

```shell
# 输出的重定向举例: printf默认是要将数据打印到终端, 可以修改默认的输出位置 => 重定向到某个文件中
# 关键字 >
# 执行一个shell指令, 获得一个输出, 这个输出默认显示到终端, 如果要将其保存到文件中, 就可以使用重定向
# 如果当前目录下test.txt不存在, 会被创建, 如果存在, 内容被覆盖
qy@ubuntu:~/linux_command_train$ ll
总用量 36
drwxrwxr-x  3 qy qy  4096 8月  15 17:03 ./
drwxr-xr-x 24 qy qy  4096 8月  15 17:01 ../
drwxrwxr-x  2 qy qy  4096 8月  15 16:07 bin/
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
qy@ubuntu:~/linux_command_train$ date > test.txt
qy@ubuntu:~/linux_command_train$ cat test.txt 
2024年 08月 15日 星期四 17:03:25 CST
qy@ubuntu:~/linux_command_train$ date >> test.txt
qy@ubuntu:~/linux_command_train$ cat test.txt 
2024年 08月 15日 星期四 17:03:25 CST
2024年 08月 15日 星期四 17:03:38 CST
```

