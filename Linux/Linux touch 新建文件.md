## 创建一个新的空文件

```shell
touch 文件名
```

​	使用 `touch` 命令可以创建一个新的空文件，**如果指定的文件是已存在的，只会更新文件的修改日期**，对内容没有任何影响。

```shell
qy@ubuntu:~/linux_command_train$ ll
总用量 40
drwxrwxr-x  3 qy qy  4096 8月  15 17:03 ./
drwxr-xr-x 24 qy qy  4096 8月  15 17:01 ../
drwxrwxr-x  2 qy qy  4096 8月  15 16:07 bin/
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
-rw-rw-r--  1 qy qy   127 8月  15 17:07 test.txt
qy@ubuntu:~/linux_command_train$ cat test.txt 
2024年 08月 15日 星期四 17:03:25 CST
2024年 08月 15日 星期四 17:03:38 CST
/home/qy/linux_command_train
/usr/bin/ls
qy@ubuntu:~/linux_command_train$ touch test.txt 
qy@ubuntu:~/linux_command_train$ ll
总用量 40
drwxrwxr-x  3 qy qy  4096 8月  15 17:03 ./
drwxr-xr-x 24 qy qy  4096 8月  15 17:01 ../
drwxrwxr-x  2 qy qy  4096 8月  15 16:07 bin/
-rwxrwxr-x  1 qy qy 17368 8月  10 16:30 myprogram*
-rw-rw-r--  1 qy qy   236 8月  15 15:14 myprogram.c
-rw-rw-r--  1 qy qy   127 8月  15 17:08 test.txt
qy@ubuntu:~/linux_command_train$ cat test.txt 
2024年 08月 15日 星期四 17:03:25 CST
2024年 08月 15日 星期四 17:03:38 CST
/home/qy/linux_command_train
/usr/bin/ls
```

