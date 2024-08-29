## 修改文件权限

​	文件权限针对 **文件所有者**、**文件所属组用户**、**其他人**这三类人而言的，对应的操作指令是 `chmod`。设置方式也有两种，分为 **文字设定法** 和 **数字设定法**。

​	文字设定法，是通过一些关键字 `r` 、`w`、`x`、`-` 来描述用户对文件的操作权限。

​	数字设定法，是通过一些数字 `0`、`1`、`2`、`4`、`5`、`6`、`7` 来描述用户对文件的操作权限。

## 文字设定法

```shell
#chmod
# 语法格式: chmod who [+|-|=] mod 文件名
	- who:
		- u: user  -> 文件所有者
		- g: group -> 文件所属组用户
		- o: other -> 其他
		- a: all, 以上是三类人 u+g+o
	- 对权限的操作:
		+: 添加权限
		-: 去除权限
		=: 权限的覆盖
	- mod: 权限
		r: read, 读
		w: write, 写
		x: execute, 执行
		-: 没有权限
		
# 将文件所有者权限设置为读和执行, 也就是权限覆盖
$ chmod u=rx b.txt 
$ ll b.txt         
-r-xrw-r-- 2 robin robin 2929 Apr 14 18:53 b.txt*

# 给其他人添加写和执行权限
$ chmod o+wx b.txt 
$ ll b.txt         
-r-xrw-rwx 2 robin robin 2929 Apr 14 18:53 b.txt*

# 给文件所属组用户去掉读和执行权限
$ chmod g-rx b.txt 
$ ll b.txt         
-r-x-w-rwx 2 robin robin 2929 Apr 14 18:53 b.txt*

# 将文件所有者,文件所属组用户,其他人权限设置为读+写+执行
$ chmod a=rwx b.txt
$ ll b.txt 
-rwxrwxrwx 2 robin robin 2929 Apr 14 18:53 b.txt*
```

## 数字设定法

| 数字 |     权限描述     | 权限 |
| :--: | :--------------: | :--: |
|  0   |      无权限      | ---  |
|  1   |    仅执行权限    | --x  |
|  2   |     仅写权限     | -w-  |
|  3   |   写和执行权限   | -wx  |
|  4   |     仅读权限     | r--  |
|  5   |   读和执行权限   | r-x  |
|  6   |    读和写权限    | rw-  |
|  7   | 读、写和执行权限 | rwx  |



```shell
# 语法格式: chmod [+|-|=] mod 文件名
	- 对权限的操作:
		+: 添加权限
		-: 去除权限
		=: 权限的覆盖, 等号可以不写
	- mod: 权限描述, 所有权限都放开是 7
		- 4: read, r
		- 2: write, w
		- 1: execute , x
		- 0: 没有权限
		
# 分解: chmod 0567 a.txt

    0           5           6             7
  八进制     文件所有者  文件所属组用户    其他人
              r + x       r + w         r+w+x

######################### 举例 #########################
# 查看文件 c.txt 的权限			   
$ ll c.txt 
-rwxrwxrwx 2 robin robin 2929 Apr 14 18:53 c.txt*

# 文件所有者去掉执行权限, 所属组用户去掉写权限, 其他人去掉读+写权限
$ chmod -123 c.txt
$ ll c.txt 
-rw-r-xr-- 2 robin robin 2929 Apr 14 18:53 c.txt*

# 文件所有者添加执行权限, 所属组用户和其他人权限不变
$ chmod +100 c.txt
$ ll c.txt 
-rwxr-xr-- 2 robin robin 2929 Apr 14 18:53 c.txt*

# 将文件所有者,文件所属组用户,其他人权限设置为读+写, 没有执行权限
$ chmod 666 c.txt
$ ll c.txt 
-rw-rw-rw- 2 robin robin 2929 Apr 14 18:53 c.txt
```

