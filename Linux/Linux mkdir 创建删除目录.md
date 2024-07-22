## 创建单层目录

```shell
mkdir 目录名
```

## 创建多层目录

```shell
mkdir parent/child/baby1/baby2 -p
```

## 删除目录

```shell
rm 目录名 -r
```

## 强制删除目录

```shell
rm 目录名 -rf
```

```shell
qy@localhost:~$ ls
公共  视频  文档  音乐  AD              Data-Structure  vim_test
模板  图片  下载  桌面  cmakelist_test  snap
qy@localhost:~$ rm AD -rf
qy@localhost:~$ ls
公共  视频  文档  音乐  cmakelist_test  snap
模板  图片  下载  桌面  Data-Structure  vim_test
```

