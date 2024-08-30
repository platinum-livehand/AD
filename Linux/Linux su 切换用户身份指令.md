## su 命令 

​	Linux 是一个多用户的操作系统，可以同时登录多个用户，因此很多时候需要在多个用户之间切换，用户切换需要使用 `su(switch user)` 或者 `su -`。使用 `su` **只切换用户**，**当前的工作目录不会变化**，但是使用 `su -` 不仅会**切换用户**也会**切换工作目录**，**工作目录切换为当前的家目录**。

## 语法

```shell
su [-] 用户名
```

- 在 Linux 中 "[ ]" 里面的内容都代表可选，可以不写。
- su：命令本体，必写。
- 用户名：参数，要切换的用户的名称。

## 普通用户的默认路径

```shell
/home/用户名
```

![](..\picture\Linux su-1.png)

## root用户的默认路径

```shell
/root
```

![](..\picture\Linux su-2.png)



## 普通用户切换 root 用户

```shell
su -
```

![](..\picture\Linux su-3.png)

```shell
su
```

![](..\picture\Linux su-4.png)

​	**可以看到 只执行`su` 只是切换了 root 身份，但是工作目录仍然是普通用户 qy 的工作目录，而使用 `su -` 命令切换后工作目录变成了 root 的工作目录。**

## root 用户切换普通用户

```shell
su - 用户名
```

![](..\picture\Linux su-5.png)

```shell
su 用户名
```

![](..\picture\Linux su-6.png)

## sudo

​	sudo(**super user do**)，sudo 执行命令的流程是当前用户切换到 root(或其他指定切换到的用户)，然后以 root (或其他指定的切换到的用户)身份执行命令，执行完成后，直接退回到当前用户；而这些的前提是要通过 sudo 的配置文件 `/etc/sudoers` 来进行授权。