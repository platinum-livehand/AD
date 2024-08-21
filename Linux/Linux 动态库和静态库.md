## 静态库

​	在 Linux 中静态库由程序 `ar` 生成，现在静态库已经不像之前那么普遍了，这主要是由于程序都在使用动态库。关于静态库的命名规则如下：

- 在 Linux 中静态库以 `lib` 作为前缀，以 `.a` 作为后缀，中间是库的名字由自己指定，即：`libxxx.a`。
- 在 Windows 中静态库一般以 `lib` 作为前缀，以 `.lib` 作为后缀，中间是库的名字由自己指定，即：`libxxx.lib`。

### 生成静态链接库

​	生成静态库，需要先对源文件进行汇编操作（使用参数 `-c`）得到二进制格式的目标文件（`.o`格式），然后再通过 `ar` 工具将目标文件打包即可得到静态库文件（`libxxx.a`)。

​	使用 `ar` 工具创建静态库的时候需要三个参数：

- 参数 `c`：创建一个库，不管库是否存在，都将创建。
- 参数 `s`：创建目标文件索引，这在创建较大的库时能加快时间。
- 参数 `r`：在库中插入模块（替换）。默认新的成员添加在库的结尾处，如果模块名已经在库中存在，则替换同名的模块。

![](..\picture\静态库-1.png)

​	生成静态库的具体步骤如下：

- 需要将源文件进行汇编，得到 `.o` 文件，需要使用参数 `-c`。

```shell
# 执行如下操作, 默认生成二进制的 .o 文件
# -c 参数位置没有要求
$ gcc 源文件(*.c) -c	
```

- 将得到的 `.o` 文件打包，得到静态库。

```shell
$ ar rcs 静态库的名字(libxxx.a) 原材料(*.o)
```

- 发布静态库。

```shell
# 发布静态库
	1. 提供头文件 **.h
	2. 提供制作出来的静态库 libxxx.a
```

### 生成静态库

- 目录

```cmd
# 目录结构 add.c div.c mult.c sub.c -> 算法的源文件, 函数声明在头文件 head.h
# main.c中是对接口的测试程序, 制作库的时候不需要将 main.c 算进去

.
├── add.c
├── div.c
├── include
│   └── head.h
├── main.c
├── mult.c
├── sub.c
└── uselib
```

- `add.c`

```c
#include <stdio.h>
#include "head.h"

int add(int a, int b)
{
    return a+b;
}
```

- `sub.c`

```c
#include <stdio.h>
#include "head.h"

int subtract(int a, int b)
{
    return a-b;
}
```

- `mult.c`

```c
#include <stdio.h>
#include "head.h"

int multiply(int a, int b)
{
    return a*b;
}
```

- `div.c`

```c
#include <stdio.h>
#include "head.h"

double divide(int a, int b)
{
    return (double)a/b;
}
```

- `head.h`

```c
#ifndef _HEAD_H
#define _HEAD_H
// 加法
int add(int a, int b);
// 减法
int subtract(int a, int b);
// 乘法
int multiply(int a, int b);
// 除法
double divide(int a, int b);
#endif
```

- `main.c`

```c
#include <stdio.h>
#include "head.h"

int main()
{
    int a = 20;
    int b = 12;
    printf("a = %d, b = %d\n", a, b);
    printf("a + b = %d\n", add(a, b));
    printf("a - b = %d\n", subtract(a, b));
    printf("a * b = %d\n", multiply(a, b));
    printf("a / b = %f\n", divide(a, b));
    return 0;
}
```

- 汇编

```cmd
# 第一步，汇编，生成 .o 文件
$ gcc *.c -c -I ./include/
$ tree
.
├── add.c
├── add.o
├── div.c
├── div.o
├── include
│   └── head.h
├── main.c
├── main.o
├── mult.c
├── mult.o
├── sub.c
├── sub.o
└── uselib
```

- 打包

```cmd
# 第二步，将生成的目标文件通过 ar 工具打包生成静态库
$ ar rcs libcalc.a *.o
$ tree
.
├── add.c
├── add.o
├── div.c
├── div.o
├── include
│   └── head.h
├── libcalc.a
├── main.c
├── main.o
├── mult.c
├── mult.o
├── sub.c
├── sub.o
└── uselib
```

- 发布

```cmd
# 第三步，将生成的静态库 libcalc.a 和库对应的头文件 head.h 一并发布给使用者即可
# 3. 发布静态库
	1. head.h    => 函数声明
	2. libcalc.a => 函数定义(二进制格式)
$ cp libcalc.a ./uselib/
$ cp ./include/head.h ./uselib/
$ cp main.c ./uselib/
$ cd ./uselib/
$ tree
.
├── head.h
├── libcalc.a
└── main.c
```

### 静态库的使用

​	当我们得到了一个可用的静态库之后，需要将其放到一个目录中，然后根据得到的头文件编写测试代码，对静态库中的函数进行调用。

```cmd
# 编译的时候指定库信息
	-L: 指定库所在的目录(相对或者绝对路径)
	-l: 指定库的名字, 掐头(lib)去尾(.a) ==> calc
# -L -l, 参数和参数值之间可以有空格, 也可以没有  -L./ -lcalc
$ gcc -o app main.c -L ./ -l calc
$ tree
.
├── app
├── head.h
├── libcalc.a
└── main.c
$ ./app
a = 20, b = 12
a + b = 32
a - b = 8
a * b = 240
a / b = 1.666667
```

## 动态库

​	动态链接库是程序运行时加载的库，当动态链接库正确部署之后，运行的多个程序可以使用同一个加载到内存中的动态库，因此在 Linux 中动态链接库也可以称之为共享库。

​	动态链接库时目标文件的集合，目标文件在动态链接库中的组织方式时按照特殊方式形成的。库中函数和变量的地址使用的是相对地址（静态库中使用的是绝对地址），其真实地址实在应用程序加载动态库时形成的。

- 在 Linux 中动态库以 `lib` 作为前缀，以 `.so` 作为后缀，中间是库的名字自己指定即可，即：`libxxx.so`。
- 在 Windows 中动态库一般以 `lib` 作为前缀，以 `.dll` 作为后缀，中间是库的名字需要自己指定，即：`libxxx.dll`。

### 生成动态链接库

​	生成动态链接库是直接使用 gcc 命令并且需要添加 `-fPIC(-fpic)`以及 `-shared` 参数。

- `-fPIC` 或 `-fpic`参数的作用是 使得 gcc 生成的代码是与位置无关的，也就是使用相对位置。
- `-shared` 参数的作用是告诉编译器生成一个动态链接库。

![](..\picture\动态库-1.png)

​	生成动态连接库的具体步骤如下：

- 将源文件进行汇编操作，需要使用参数 `-c`，还需要添加额外参数 `-fpic/-fPIC`。

```cmd
# 得到若干个 .o文件
$ gcc 源文件(*.c) -c -fpic
```

- 将得到的 `.o` 文件打包成动态库，还是使用 gcc，使用参数 `-shared` 指定生成动态库（位置没有要求）。

```cmd
$ gcc -shared 与位置无关的目标文件(*.o) -o 动态库(libxxx.so)
```

- 发布动态库和头文件

```cmd
# 发布
 	1. 提供头文件: xxx.h
 	2. 提供动态库: libxxx.so
```

### 动态库生成举例

- 目录

```cmd
$ tree
.
├── add.c
├── div.c
├── include
│   └── head.h
├── main.c
├── mult.c
├── sub.c
└── useso
```

- 汇编

```cmd
$ gcc *.c -c -fpic -I ./include/
$ tree
.
├── add.c
├── add.o
├── div.c
├── div.o
├── include
│   └── head.h
├── main.c
├── mult.c
├── mult.o
├── sub.c
├── sub.o
└── useso
```

- 打包

```cmd
qy@ubuntu:~/linux_command_train/testso$ gcc -o libcalc.so *.o -shared
qy@ubuntu:~/linux_command_train/testso$ tree
.
├── add.c
├── add.o
├── div.c
├── div.o
├── include
│   └── head.h
├── libcalc.so
├── main.c
├── mult.c
├── mult.o
├── sub.c
├── sub.o
└── useso
```

- 发布

```cmd
$ cp libcalc.so ./useso/
$ cp ./include/head.h ./useso/
$ cd ./useso/
$ cp ../main.c ./
$ tree
.
├── head.h
├── libcalc.so
└── main.c
```

### 动态库的使用

​	当我们得到了一个可用的动态库之后，需要将其放到一个目录中，然后根据得到的头文件编写测试代码，对动态库中的函数进行调用。

```cmd
# 1. 拿到发布的动态库
	`head.h   libcalc.so
# 2. 基于头文件编写测试程序, 测试动态库中提供的接口是否可用
	`main.c`
# 示例目录:
.
├── head.h          ==> 函数声明
├── libcalc.so      ==> 函数定义
└── main.c          ==> 函数测试
```

```cmd
$ gcc -o test main.c -L ./ -l calc
$ tree
.
├── head.h
├── libcalc.so
├── main.c
└── test
$ ./test
a = 20, b = 12
a + b = 32
a - b = 8
a * b = 240
a / b = 1.666667
```

### 查看可执行程序依赖的动态链接库

```cmd
# 语法:
$ ldd 可执行程序名

# 举例:
# 成功
$ ldd ./test
	linux-vdso.so.1 (0x00007ffc4c18d000)
	libcalc.so (0x00007fc082583000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fc08237b000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fc08258f000)
# 失败	
$ ldd ./test
linux-vdso.so.1 (0x00007ffeff3ac000)
libcalc.so => not found
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f84ecf08000)
/lib64/ld-linux-x86-64.so.2 (0x00007f84ed117000)
```

## 优缺点

### 静态库

- 优点：

  - 静态库被打包到应用程序中加载速度快。
  - 发布程序无需提供静态库，移植方便。

- 缺点：

  - 相同的库文件数据可能在内存中被加载多份，消耗系统资源，浪费内存。
  - 库文件更新需要重新编译项目文件，生成新的可执行程序，浪费时间。

  ![](..\picture\静态库-2.png)

### 动态库

- 优点：
  - 可实现不同进程间的资源共享。
  - 动态库升级简单，只需要替换库文件，无需重新编译应用程序。
  - 我们可以控制何时加载动态库，不调用库函数动态库就不会被加载。
- 缺点：
  - 加载速度比静态库满，以现在计算机的性能可以忽略。
  - 发布程序需要提供依赖的动态库。

![](..\picture\动态库-2.png)