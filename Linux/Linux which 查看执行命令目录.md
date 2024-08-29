## which 命令

​	which 命令可以查看要执行的命令所在的实际路径，命令解析器工作的时候也会搜索这个目录。**需要注意的是该命令只能查看非内建的 shell 指令所在的实际路径，有些命令是写在内核中的，无法查看**。

​	我们使用的大部分 shell 命令都是放在系统的 `/usr/bin` 或者 `/usr/sbin` 目录下。

```shell
# 由于使用的Linux版本不同, 得到的路径也会有不同
$ which ls
/usr/bin/ls
$ which tree
/usr/bin/tree
$ which ssh
/usr/bin/ssh
$ which sudo
/usr/bin/sudo
$ which reboot
/usr/sbin/reboot
$ which shutdown
/usr/sbin/shutdown
```

