## sftp

​	SFTP（SSH File Transfer Protocol）是一种安全的文件传输协议，通常用在计算机之间安全地传输文件。

## 连接到 SFTP 服务器

```shell
sftp username@hostname
```

## 列出远程目录内容

```shell
ls
```

## 列出本地目录内容

```shell
lls
```

## 更改远程目录

```shell
cd directory_name
```

## 更改本地目录

```shell
lcd directory_name
```

## 上传文件到远程服务器

​	**指定本地文件的绝对路径，上传到服务器的当前路径**，使用 pwd 查看远程服务器当前路径。

```shell
put /path/to/local/local_file_path.txt
```

## 下载文件到本地

​	**指定远程服务器的文件的绝对路径**，下载到本地的当前路径，使用 lpwd 查看本地当前路径。

```shell
get /path/to/remote/remote_file_path.txt
```

## 退出 SFTP 会话

```shell
exit
```

