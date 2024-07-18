## 列出当前目录内容

```shell
ls
```

## 长格式显示文件信息

```shell
ls -l
```

```shell
root@localhost:/# ls -l
总计 3405916
lrwxrwxrwx   1 root root          7  4月 22 21:08 bin -> usr/bin
drwxr-xr-x   2 root root       4096  2月 26 20:58 bin.usr-is-merged
drwxr-xr-x   4 root root       4096  7月 10 15:36 boot
dr-xr-xr-x   2 root root       4096  4月 24 19:28 cdrom
drwxr-xr-x  19 root root       4320  7月 18 19:52 dev
drwxr-xr-x 141 root root      12288  7月 12 11:32 etc
drwxr-xr-x   3 root root       4096  7月 10 15:48 home
lrwxrwxrwx   1 root root          7  4月 22 21:08 lib -> usr/lib
lrwxrwxrwx   1 root root          9  4月 22 21:08 lib64 -> usr/lib64
drwxr-xr-x   2 root root       4096  4月  8 22:37 lib.usr-is-merged
drwx------   2 root root      16384  7月 10 14:57 lost+found
drwxr-xr-x   3 root root       4096  7月 10 15:49 media
drwxr-xr-x   2 root root       4096  4月 24 18:47 mnt
drwxr-xr-x   2 root root       4096  4月 24 18:47 opt
dr-xr-xr-x 372 root root          0  7月 18 19:51 proc
drwx------   7 root root       4096  7月 11 17:02 root
drwxr-xr-x  38 root root        960  7月 18 20:10 run
lrwxrwxrwx   1 root root          8  4月 22 21:08 sbin -> usr/sbin
drwxr-xr-x   2 root root       4096  3月 31 17:00 sbin.usr-is-merged
drwxr-xr-x  20 root root       4096  7月 11 10:13 snap
drwxr-xr-x   2 root root       4096  4月 24 18:47 srv
-rw-------   1 root root 3487563776  7月 10 15:13 swap.img
dr-xr-xr-x  13 root root          0  7月 18 20:25 sys
drwxrwxrwt  19 root root       4096  7月 18 20:22 tmp
drwxr-xr-x  12 root root       4096  4月 24 18:47 usr
drwxr-xr-x  14 root root       4096  7月 10 15:47 var
```

​	这个选项以长格式列出文件信息，包括权限、所有者、文件大小和最后修改时间等。

## 列出所有文件(包含隐藏文件)

```shell
root@localhost:/home/qy# ls -la
总计 124
drwxr-x--- 22 qy   qy   4096  7月 18 18:01 .
drwxr-xr-x  3 root root 4096  7月 10 15:48 ..
drwxr-xr-x  2 qy   qy   4096  7月 10 15:48 公共
drwxr-xr-x  2 qy   qy   4096  7月 10 15:48 模板
drwxr-xr-x  2 qy   qy   4096  7月 10 15:48 视频
drwxr-xr-x  2 qy   qy   4096  7月 10 15:48 图片
drwxr-xr-x  2 qy   qy   4096  7月 10 15:48 文档
drwxr-xr-x  3 qy   qy   4096  7月 17 10:45 下载
drwxr-xr-x  2 qy   qy   4096  7月 10 15:48 音乐
drwxr-xr-x  2 qy   qy   4096  7月 10 15:48 桌面
drwxrwxr-x 11 qy   qy   4096  7月 14 12:11 AD
-rw-------  1 qy   qy   6694  7月 18 20:15 .bash_history
-rw-r--r--  1 qy   qy    220  3月 31 16:41 .bash_logout
-rw-r--r--  1 qy   qy   3771  3月 31 16:41 .bashrc
drwx------ 15 qy   qy   4096  7月 14 12:09 .cache
drwxrwxr-x  7 qy   qy   4096  7月 17 10:07 cmakelist_test
drwx------ 14 qy   qy   4096  7月 10 21:52 .config
drwxrwxr-x 11 qy   qy   4096  7月 13 21:16 Data-Structure
drwxrwxr-x  3 qy   qy   4096  7月 10 16:36 .dotnet
-rw-rw-r--  1 qy   qy     44  7月 13 17:05 .gitconfig
-rw-------  1 qy   qy     20  7月 18 12:06 .lesshst
drwx------  4 qy   qy   4096  7月 10 15:48 .local
drwx------  3 qy   qy   4096  7月 10 16:12 .pki
-rw-r--r--  1 qy   qy    807  3月 31 16:41 .profile
drwx------  9 qy   qy   4096  7月 17 10:45 snap
drwx------  2 qy   qy   4096  7月 10 15:49 .ssh
-rw-r--r--  1 qy   qy      0  7月 10 16:48 .sudo_as_admin_successful
drwxrwxr-x  2 qy   qy   4096  7月 18 18:01 test_vim
-rw-------  1 qy   qy   5792  7月 18 18:01 .viminfo
drwxrwxr-x  4 qy   qy   4096  7月 10 16:12 .vscode
```

## 按文件大小排序

```shell
ls -lS
```

```shell
root@localhost:/home/qy/Data-Structure# ls -lS
总计 40
drwxrwxr-x 2 qy qy 4096  7月 18 12:02 bin
drwxrwxr-x 5 qy qy 4096  7月 18 10:50 build
drwxrwxr-x 2 qy qy 4096  7月 13 16:01 docs
drwxrwxr-x 2 qy qy 4096  7月 13 15:48 lib
drwxrwxr-x 7 qy qy 4096  7月 18 10:47 src
drwxrwxr-x 2 qy qy 4096  7月 13 17:00 test
drwxrwxr-x 2 qy qy 4096  7月 13 17:00 third_party
-rw-rw-r-- 1 qy qy 1230  7月 17 11:02 CMakeLists.txt
-rw-rw-r-- 1 qy qy 1059  7月 13 15:47 LICENSE
-rw-rw-r-- 1 qy qy   29  7月 13 15:47 README.md
```

## 按时间排序（最近修改的文件排在前面)

```shell
ls -lt
```

```shell
root@localhost:/home/qy/Data-Structure# ls -lt
总计 40
drwxrwxr-x 2 qy qy 4096  7月 18 12:02 bin
drwxrwxr-x 5 qy qy 4096  7月 18 10:50 build
drwxrwxr-x 7 qy qy 4096  7月 18 10:47 src
-rw-rw-r-- 1 qy qy 1230  7月 17 11:02 CMakeLists.txt
drwxrwxr-x 2 qy qy 4096  7月 13 17:00 test
drwxrwxr-x 2 qy qy 4096  7月 13 17:00 third_party
drwxrwxr-x 2 qy qy 4096  7月 13 16:01 docs
drwxrwxr-x 2 qy qy 4096  7月 13 15:48 lib
-rw-rw-r-- 1 qy qy 1059  7月 13 15:47 LICENSE
-rw-rw-r-- 1 qy qy   29  7月 13 15:47 README.md
```

## 以人类可读的格式显示文件大小

```shell
ls -lh
```

```shell
root@localhost:/home/qy/Data-Structure# ls -lh
总计 40K
drwxrwxr-x 2 qy qy 4.0K  7月 18 12:02 bin
drwxrwxr-x 5 qy qy 4.0K  7月 18 10:50 build
-rw-rw-r-- 1 qy qy 1.3K  7月 17 11:02 CMakeLists.txt
drwxrwxr-x 2 qy qy 4.0K  7月 13 16:01 docs
drwxrwxr-x 2 qy qy 4.0K  7月 13 15:48 lib
-rw-rw-r-- 1 qy qy 1.1K  7月 13 15:47 LICENSE
-rw-rw-r-- 1 qy qy   29  7月 13 15:47 README.md
drwxrwxr-x 7 qy qy 4.0K  7月 18 10:47 src
drwxrwxr-x 2 qy qy 4.0K  7月 13 17:00 test
drwxrwxr-x 2 qy qy 4.0K  7月 13 17:00 third_party
```

