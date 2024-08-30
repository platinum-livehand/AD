## 简介

​	GCC 是 Linux 下的编译工具集，是 GNU Compiler Collection 的缩写，包含 gcc、g++ 等编译器。

## 安装 gcc

```shell
# 安装软件必须要有管理员权限
# ubuntu
$ sudo apt update   		# 更新本地的软件下载列表, 得到最新的下载地址
$ sudo apt install gcc g++	# 通过下载列表中提供的地址下载安装包, 并安装
```

```shell
# 查看 gcc 版本
$ gcc -v
$ gcc --version

# 查看 g++ 版本
$ g++ -v
$ g++ --version
```

## gcc 工作流程

​	GCC 编译器对程序的编译，分为四个阶段：**预处理（预编译）**、**编译和优化**、**汇编**和**链接**。

![](..\picture\编译过程.png)

- 预处理：在这个阶段主要做三件事：头文件展开、宏替换、去注释。这个阶段需要 GCC 调用**预处理器**来完成，最终得到的还是源文件。
- 编译：这个阶段需要 GCC 调用**编译器**对文件进行编译，最终得到一个汇编文件。
- 汇编：这个阶段需要 GCC 调用**汇编器**对文件进行汇编，最终得到一个二进制文件。
- 链接：这个阶段需要 GCC 调用**链接器**对程序需要调用的库进行链接，最终得到也给可执行的二进制文件。

| 文件后缀 |              说明              | gcc 参数 |
| :------: | :----------------------------: | :------: |
|   `.c`   |             源文件             |    无    |
|   `.i`   |       预处理后的 C 文件        |   `-E`   |
|   `.s`   | 编译之后得到的汇编语言的源文件 |   `-S`   |
|   `.o`   |     汇编后得到的二进制文件     |    -c    |

​	在 Linux 下直接使用 gcc 命令后面加上要编译的 C 语言的源文件，GCC 会自动生成文件名为 `a.out`的可执行文件（也可以通过参数 -o 指定生成的文件名）。也可以单步执行。

### 头文件 string.h

```c
#ifndef _STRING_H_
#define _STRING_H_
int strLength(char *string);
#endif // _STRING_H_
```

### 源文件 string.c

```c
#include "string.h"

int strLength(char *string)
{
	int len = 0;
	while(*string++ != '\0') 	// 当*string 的值为'\0'时, 停止计算
    {
        len++;
    }
	return len; 	// 返回字符串长度
}
```

### 源文件 main.c

```c
#include <stdio.h>
#include "string.h"

int main(void)
{
	char *src = "Hello, I'am Monkey·D·Luffy!!!"; 
	printf("string length is: %d\n", strLength(src)); 
	return 0;
}
```

## 单步编译

### 预处理

​	对源文件进行预处理，需要使用的 gcc 参数为 `-E`。

```shell
$ gcc -o string.i -E string.c
$ gcc -o main.i -E main.c
$ ll
总用量 44
drwxrwxr-x 2 qy qy  4096 8月  19 15:51 ./
drwxrwxr-x 4 qy qy  4096 8月  19 15:39 ../
-rw-rw-r-- 1 qy qy   177 8月  19 09:17 main.c
-rw-rw-r-- 1 qy qy 16473 8月  19 15:51 main.i
-rw-rw-r-- 1 qy qy   143 8月  19 09:10 string.c
-rw-rw-r-- 1 qy qy    77 8月  19 09:07 string.h
-rw-rw-r-- 1 qy qy   342 8月  19 15:50 string.i
```

### 编译

​	编译预处理之后的文件，需要使用的 gcc 参数为 `-S`。

```shell
$ gcc -o string.s -S string.i
$ gcc -o main.s -S main.i
$ ll
总用量 52
drwxrwxr-x 2 qy qy  4096 8月  19 15:53 ./
drwxrwxr-x 4 qy qy  4096 8月  19 15:39 ../
-rw-rw-r-- 1 qy qy   177 8月  19 09:17 main.c
-rw-rw-r-- 1 qy qy 16473 8月  19 15:51 main.i
-rw-rw-r-- 1 qy qy   859 8月  19 15:53 main.s
-rw-rw-r-- 1 qy qy   143 8月  19 09:10 string.c
-rw-rw-r-- 1 qy qy    77 8月  19 09:07 string.h
-rw-rw-r-- 1 qy qy   342 8月  19 15:50 string.i
-rw-rw-r-- 1 qy qy   783 8月  19 15:53 string.s
```

### 汇编

​	对得到的汇编文件进行汇编，需要使用的 gcc 参数为 `-c`。

```shell
$ gcc -c string.s
$ gcc -c main.s
$ ll
总用量 60
drwxrwxr-x 2 qy qy  4096 8月  19 15:55 ./
drwxrwxr-x 4 qy qy  4096 8月  19 15:39 ../
-rw-rw-r-- 1 qy qy   177 8月  19 09:17 main.c
-rw-rw-r-- 1 qy qy 16473 8月  19 15:51 main.i
-rw-rw-r-- 1 qy qy  1840 8月  19 15:55 main.o
-rw-rw-r-- 1 qy qy   859 8月  19 15:53 main.s
-rw-rw-r-- 1 qy qy   143 8月  19 09:10 string.c
-rw-rw-r-- 1 qy qy    77 8月  19 09:07 string.h
-rw-rw-r-- 1 qy qy   342 8月  19 15:50 string.i
-rw-rw-r-- 1 qy qy  1408 8月  19 15:55 string.o
-rw-rw-r-- 1 qy qy   783 8月  19 15:53 string.s
```

### 链接

​	将的到的二进制文件和标准库进行链接，得到可执行的二进制文件，不需要任何参数。

```shell
$ gcc -o test string.o main.o
$ ll
总用量 80
drwxrwxr-x 2 qy qy  4096 8月  19 15:56 ./
drwxrwxr-x 4 qy qy  4096 8月  19 15:39 ../
-rw-rw-r-- 1 qy qy   177 8月  19 09:17 main.c
-rw-rw-r-- 1 qy qy 16473 8月  19 15:51 main.i
-rw-rw-r-- 1 qy qy  1840 8月  19 15:55 main.o
-rw-rw-r-- 1 qy qy   859 8月  19 15:53 main.s
-rw-rw-r-- 1 qy qy   143 8月  19 09:10 string.c
-rw-rw-r-- 1 qy qy    77 8月  19 09:07 string.h
-rw-rw-r-- 1 qy qy   342 8月  19 15:50 string.i
-rw-rw-r-- 1 qy qy  1408 8月  19 15:55 string.o
-rw-rw-r-- 1 qy qy   783 8月  19 15:53 string.s
-rwxrwxr-x 1 qy qy 16768 8月  19 15:56 test*
```

## 直接编译

```shell
$ gcc -o test *.c
$ ll
总用量 40
drwxrwxr-x 2 qy qy  4096 8月  19 16:02 ./
drwxrwxr-x 4 qy qy  4096 8月  19 15:39 ../
-rw-rw-r-- 1 qy qy   177 8月  19 09:17 main.c
-rw-rw-r-- 1 qy qy   143 8月  19 09:10 string.c
-rw-rw-r-- 1 qy qy    77 8月  19 09:07 string.h
-rwxrwxr-x 1 qy qy 16768 8月  19 16:02 test*
```

## gcc 常用参数

|       gcc编译选线        |                          选项的意义                          |
| :----------------------: | :----------------------------------------------------------: |
|           `-E`           |                预处理指定的源文件，不进行编译                |
|           `-S`           |               编译指定的源文件，但是不进行汇编               |
|         **`-c`**         |            编译、汇编指定的源文件，但是不进行链接            |
| **`-o [file1] [file2]`** |                将文件 `file2` 编译成 `file1`                 |
|         **`-I`**         |              指定 `include` 包含文件的搜索目录               |
|         **`-g`**         |      在编译的时候，生成调式信息，该程序可以被调试器调试      |
|         **`-D`**         |                 在程序编译的时候，指定一个宏                 |
|           `-w`           |     不生成任何警告信息，不建议使用，有些时候警告就是错误     |
|       **`-Wall`**        |                       生成所有警告信息                       |
|        **`-On`**         | n 的取值范围：0-3。编译器的优化选项的 4 个级别，-O0表示没有优化，-O3优化级别最高 |
|         **`-l`**         |                在程序编译的时候，指定使用的库                |
|         **`-L`**         |                 指定编译的时候，搜索库的路径                 |
|     **`-fPIC/fpic`**     |                     生成与位置无关的代码                     |
|      **`-shared`**       |            生成共享目标文件，通常用在建立共享库时            |
|        **`-std`**        |   指定 C 语言标准，如：-std = c99，gcc 默认的标准是 GNU C    |

## 搜索头文件（`-I`）

​	如果在程序中包含了一些头文件，但是包含的一些头文件在程序处理的时候因为找不到无法被展开，导致程序编译失败，这时候我们可以在 gcc 命令中添加 `-I`参数重新指定要引用的头文件路径，保证编译顺利完成。

```shell
# -I, 指定头文件目录
$ tree
.
├── add.c
├── div.c
├── include
│   └── head.h
├── main.c
├── mult.c
└── sub.c

# 编译当前目录中的所有源文件，得到可执行程序
$ gcc *.c -o calc
main.c:2:18: fatal error: head.h: No such file or directory
compilation terminated.
sub.c:2:18: fatal error: head.h: No such file or directory
compilation terminated.
```

​	通过编译得到的错误信息可以知道，源文件中包含的头文件无法被找到。通过提供的目录结构可以的只头文件 head.h 在 include 目录中，因此可以在编译的时候重新指定偷吻加你，具体操作如下：

```shell
# 可以在编译的时候重新指定头文件位置 -I 头文件目录
$ gcc *.c -o calc -I ./include
```

## 指定一个宏（`-D`）

​	在程序中我们可以使用宏定义一个常量，也可以通过宏控制某段代码是否被执行。在下面这段程序中第 8 行判断是否定义了一个叫做 DEBUG 的宏，如果没有定义第 9 行代码就不会被执行。

```shell
// test.c
#include <stdio.h>
#define NUMBER  3

int main()
{
    int a = 10;
#ifdef DEBUG
    printf("我是一个程序猿, 我不会爬树...\n");
#endif
    for(int i=0; i<NUMBER; ++i)
    {
        printf("hello, GCC!!!\n");
    }
    return 0;
}
```

​	如果不想在程序中定义这个宏，但是又想让它存在，通过 gcc 参数 `-D` 就可以实现了，编译器会认为参数后边指定的宏在程序中是存在的。

```shell
# 在编译命令中定义这个 DEBUG 宏, 
$ gcc test.c -o app -D DEBUG

# 执行生成的程序， 可以看到程序第9行的输出
$ ./app 
我是一个程序猿, 我不会爬树...
hello, GCC!!!
hello, GCC!!!
hello, GCC!!!
```

### `-D`参数的应用场景：

​	在发布程序的时候，一般都会要求将程序中所有的 log 输出去掉，如果不去掉会影响程序的执行效率，很显然删除这些打印 log 的源代码是一件很麻烦的事情，解决方案是这样的：

- 将所有的打印 log 的代码都写到一个宏判定中。
- 在编译程序的时候指定 `-D` 就会有 `log` 输出。
- 在编译程序的时候不指定 `-D`就不会有 `log` 输出。