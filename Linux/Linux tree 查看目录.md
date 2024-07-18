## 查看当前目录的结构，n为显示的目录层数

```shell
tree [-L n]
```

## 查看指定目录的结构，n为显示的目录层数

```shell
tree 目录名 [-L -n]
```

```shell
# 只显示1层
[root@VM-8-14-centos ~]# tree -L 1
.
|-- ace
|-- file
|-- ipc.tar.gz
|-- link.lnk -> /root/luffy/onepiece.txt
`-- luffy

# 显示2层目录
[root@VM-8-14-centos ~]# tree -L 2
.
|-- ace
|   `-- brother
|-- file
|   |-- dir
|   |-- haha.tar.gz
|   |-- hello
|   |-- link -> /root/file/test
|   |-- pipe-2
|   |-- subdir
|   `-- test
|-- ipc.tar.gz
|-- link.lnk -> /root/luffy/onepiece.txt
`-- luffy
    |-- get
    `-- onepiece.txt
```

