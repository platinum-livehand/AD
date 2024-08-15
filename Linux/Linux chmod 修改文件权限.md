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
robin@OS:~/Linux$ chmod u=rx b.txt 
robin@OS:~/Linux$ ll b.txt         
-r-xrw-r-- 2 robin robin 2929 Apr 14 18:53 b.txt*

# 给其他人添加写和执行权限
robin@OS:~/Linux$ chmod o+wx b.txt 
robin@OS:~/Linux$ ll b.txt         
-r-xrw-rwx 2 robin robin 2929 Apr 14 18:53 b.txt*

# 给文件所属组用户去掉读和执行权限
robin@OS:~/Linux$ chmod g-rx b.txt 
robin@OS:~/Linux$ ll b.txt         
-r-x-w-rwx 2 robin robin 2929 Apr 14 18:53 b.txt*

# 将文件所有者,文件所属组用户,其他人权限设置为读+写+执行
robin@OS:~/Linux$ chmod a=rwx b.txt
robin@OS:~/Linux$ ll b.txt 
-rwxrwxrwx 2 robin robin 2929 Apr 14 18:53 b.txt*
```

