## 问题

​	出现提示：无法调整只读文件系统的大小，只能在挂载时调整文件系统的大小。

![](..\picture\Ubuntu扩容-1.png)

## 解决步骤

### 第一步：查看只读文件系统的详细信息，

![](..\picture\Ubuntu扩容-2.jpg)

### 第二步：查看该磁盘挂载的文件目录

![](..\picture\Ubuntu扩容-3.jpg)

### 第三步：以root权限打开终端，重新挂载文件夹目录的读写权限

```shell
sudo -i
mount -o remount -rw /
mount -o remount -rw /var/snap/firefox/common/host-hunspell
```

![](..\picture\Ubuntu扩容-4.png)

### 第四步：刷新 gparted 中的设备后，就可以调整文件系统大小了

![](..\picture\Ubuntu扩容-5.png)