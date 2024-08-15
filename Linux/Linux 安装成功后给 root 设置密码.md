## Linux 安装后需要给 root 设置密码

```shell
qy@ubuntu:/home$ su -
密码： 
su: 认证失败
qy@ubuntu:/home$ sudo passwd root
[sudo] qy 的密码： 
新的 密码： 
重新输入新的 密码： 
passwd：已成功更新密码
qy@ubuntu:/home$ su -
密码： 
root@ubuntu:~# 
```

