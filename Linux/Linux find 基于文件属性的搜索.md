## find 

​	`find`是 Linux 中一个搜索功能非常强大的工具，它的主要功能是根据文件的属性，查找对应的磁盘文件，比如说我们常用的一些属性，**文件名**，**文件类型**，**文件大小**，**文件的目录深度** 等。

​	如果想通过属性对文件进行搜索，只需要指定出属性对应的参数就可以了。

## 文件名（-name）

​	根据文件名进行搜索有两种方式，**精确查询** 和 **模糊查询**。关于模糊查询必须要使用对应的通配符，最常用的有两个，分别为 `*` 和 `?`。其中 `*` 可以匹配零个或者多个字符，`?`用于匹配单个字符。

​	如果我们进行模糊查询，建议将带有通配符的文件名写到引号中，这样可以避免搜索命令执行失败。

```shell
# 语法格式: 根据文件名搜索 
$ find 搜索的路径 -name 要搜索的文件名
```

```shell
# 模糊搜索
$ find ./ -name "*.c"
./myprogram.c
./testgcc.c
./temp/myprogram.c
./temp/testgcc.c

########################################################

# 精确搜索
$ find ./ -name testgcc.c
./testgcc.c
./temp/testgcc.c
```

## 文件类型（-type）

​	Linux 中有 7 种文件类型，如果有需求我们可以通过 `find` 对指定类型的文件进行搜索，该属性对应的参数为 -type。其中每种类型都有对应的关键字。

|    文件类型    | 类型的字符描述 |
| :------------: | :------------: |
|  普通文件类型  |       f        |
|    目录类型    |       d        |
|   软链接类型   |       l        |
|  字符设备类型  |       c        |
|   块设备类型   |       b        |
|    管道类型    |       p        |
| 本地套接字类型 |       s        |

```shell
# 语法格式: 
$ find 搜索的路径 -type 文件类型
```

```shell
# 搜索软连接类型的文件
$ tree -L 2
.
├── all.tar.gz
├── myprogram
├── myprogram.c
├── part.tar.bz2
├── src.zip
├── temp
│   ├── myprogram.c
│   ├── testgcc.c
│   └── test.lnk -> myprogram
├── testgcc
├── testgcc.c
├── testTag.txt
└── test.txt

1 directory, 12 files
$ find ./ -type l
./temp/test.lnk

```

## 文件大小

​	如果需要根据文件大小进行搜索，需要使用参数 `-size`。关于文件大小的单位有很多，可以根据实际需求选择，常用的分别有 k（小写）、M（大写）、G（大写）。

```shell
# 语法格式: 
$ find 搜索的路径 -size [+|-]文件大小
	- 文件大小需要加单位: 
		- k (小写)
		- M (大写)
		- G (大写)
```

- `-size 4k` 表示的区间为 `(4-1k, 4k]`，表示一个区间，大于3k，小于等于4k。
- `-size -4k`表示的区间为 `[0k, 4-1k]`，表示一个区间，大于等于0，并且小于等于3k。
- `-size +4k` 表示的区间为 (4k, 正无穷)，表示搜索大于4k的文件。

```shell
# 搜索当前目录下 大于3M的所有文件 (file>3M)
$ find ./ -size +3M

# 搜索当前目录下 大于等于0M并且小于等于2M的文件 (0M <= file <=2M)
$ find ./ -size -3M

# 搜索当前目录下 大于2M并且小于等于3M的文件 (2M < file <=3M)
$ find ./ -size 3M

# 搜索当前目录下 大于1M 并且 小于等于 3M 的文件
$ find ./ -size +1M -size -4M
```

## 目录层级

​	因为 Linux 的目录是树状结构，所有目录可能有很多层，在搜索某些属性的时候可以指定只搜索几层目录，相关的参数有两个，分别是：`-maxdepth` 和 `-mindepth`。这两个参数不能单独使用，必须和其他属性一起使用，也就是搜索几层目录种满足条件的文件。

- `-maxdepth`：最多搜索到多少层目录。
- `-mindepth`：至少从第多少层开始搜索。

```shell
# 必须先指定 -maxdepth 或者 -mindepth 属性，才能指定其他属性
$ sudo find / -maxdepth 1 -type l
[sudo] qy 的密码： 
/bin
/lib64
/lib32
/libx32
/lib
/sbin
```

## 同时执行多个操作

​	在搜索文件的时候如果想在一个 `find` 执行多个操作，通过使用管道 `|` 的方式是行不通的。

```shell
# 比如: 通过find搜索目录中后缀为 .txt 的文件, 然后再查看这些满足条件的文件的详细信息
# 在find操作中直接通过管道操作多个指令, 最终输出的结果是有问题, 因此不能直接这样使用
$ find ./ -name "*.txt" | ls -l
总用量 52
-rwxrwxr-x 1 qy qy 17368 8月  10 16:30 myprogram
-rw-rw-r-- 1 qy qy   236 8月  15 15:14 myprogram.c
-rwxrwxr-x 1 qy qy 17096 8月  15 21:53 testgcc
-rw-rw-r-- 1 qy qy  1820 8月  15 21:53 testgcc.c
-rw-rw-r-- 1 qy qy  2982 8月  15 21:32 testTag.txt
```

​	如果想要实现上面的需求，需要在 `find` 中使用 `exec`，`ok`，`xargs`，这样就可以在 find 命令执行完毕之后，再执行其他子命令。

### exec

​	`-exec` 是 `find` 的参数，可以再 `exec` 参数后添加其他需要被执行的 shell 命令。find 添加了 exec 参数之后，命令的尾部需要添加后缀 `{ } \;`，注意 `{ }` 和 `\` 之间需要有一个空格。

​	在参数 `-exec` 后添加的 shell 命令处理的是 `find` 搜索之后的结果，`find` 的结果会作为新添加的 shell 命令的输入，最后在终端上输出最终的处理结果。 

```shell
# 语法：
$ find 路径 参数 参数值 -exec shell命令2 {} \;
```

```shell
$ find ./ -name "*.txt"
./testTag.txt
$ find ./ -name "*.txt" -exec ls -l {} \;
-rw-rw-r-- 1 qy qy 2982 8月  15 21:32 ./testTag.txt
```

### ok

​	`-ok`和 `-exec` 都是 `find` 命令的参数，使用方式类似，但是这个参数是交互式的，在处理 `find` 的结果的时候，会向用户发起询问，比如在删除搜索结果的时候，为了保险起见，就需要询问机制了。

```shell
# 语法: 其实就是将 -exec 替换为 -ok, 其他都不变
$ find 路径 参数 参数值 -ok shell命令2 {} \;
```

```shell
# 搜索到了2个满足条件的文件
$ find ./ -maxdepth 1  -name "*.txt"
./aaaaa.txt 
./english.txt

# 查找并显示文件详细信息
$ find ./ -maxdepth 1  -name "*.txt" -ok ls -l {} \;     
< ls ... ./aaaaa.txt > ? y		# 同意显示文件详细信息
-rw-rw-r-- 1 robin robin 10 Apr 17 11:34 ./aaaaa.txt
< ls ... ./english.txt > ? n	# 不同意显示文件详细信息, 会跳过显示该条信息

# 什么时候需要交互呢? ---> 删除文件的时候
$ find ./ -maxdepth 1  -name "*.txt" -ok rm -rf {} \;     
< rm ... ./aaaaa.txt > ? y		# 同意删除
< rm ... ./english.txt > ? n	# 不同意删除

# 删除一个文件之后再次进行相同的搜索
$ find ./ -maxdepth 1  -name "*.txt"
./english.txt		# 只剩下了一个.txt 文件
```

### xargs

​	在使用 `find` 的 `-exec` 参数的时候，需要在指定的子命令尾部添加几个特殊字符 `{} \;`，一不小心就容易忘记，有一个看起来更加直观、书写更加简洁的方式，我们可以使用 `xargs` 替换掉 `-exec` 参数，而且在处理数据的时候 `xargs` 更高效。有了 `xargs` 的加持我们就可以在 `find` 命令中直接使用管道完成前后命令的数据传递。

```shell
# 在find 中 使用 xargs 关键字我们就可以使用管道了, 否则使用管道也不会起作用
# 将 find 搜索的结果通过管道传递给后边的shell命令继续处理
$ find 路径 参数 参数值 | xargs shell命令2
```

```shell
# 查找文件
$ find ./ -maxdepth 1  -name "*.cpp" 
./occi.cpp
./main.cpp
./test.cpp

# 查找文件, 并且显示文件的详细信息
robin@OS:~$ find ./ -maxdepth 1  -name "*.cpp" | xargs ls -l
-rw-r--r-- 1 robin robin 2223 Mar  2  2020 ./main.cpp
-rw-r--r-- 1 robin robin 1406 Mar  2  2020 ./occi.cpp
-rw-r--r-- 1 robin robin 2015 Mar  1  2020 ./test.cpp

# xargs的效率比使用 -exec 效率高
-exec:  将find查询的结果逐条传递给后边的shell命令
-xargs: 将find查询的结果一次性传递给后边的shell命令
```

- `ls -l`需要文件名作为命令行参数。如果没有提供文件名，它将列出当前目录的内容。
- 当你使用管道将 find 的输出传递给 `ls -l` 时，`ls` 不会将这些输入视为文件名，而是会忽略它们。
- `xargs`会将标准输入的内容转换为命令行参数，从而使 `ls` 能够正确处理这些文件名。
