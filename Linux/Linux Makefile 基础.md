## 简介

​	使用 GCC 命令进行程序编译在单个文件下是很方便的，当工程中的文件逐渐增多，甚至十分庞大的时候，使用 GCC 命令编译就会变得力不从心，这种情况下我们需要借助项目构造工具 make 帮助我们完成这个艰巨的任务。 make 是一个命令工具，是一个解释 makefile 中指令的命令工具，一般来说，大多数的 IDE 都有这个命令，比如：Visula C++ 的 `nmake`，QtCreator 的 `qmake` 等。

​	makefile 文件有两种命名方式 makefile 和 Makefile ，构建项目的时候在哪个目录下执行构建命令 make 这个目录下的 makefile 文件就会被加载，因此，在一个项目中可以有多个 makefile 文件，分别位于不同的项目目录中。

## 举例

- 项目结构

```shell
$ tree
.
├── add.c
├── build
├── div.c
├── head.h
├── main.c
├── makefile
├── mult.c
└── sub.c
```

- 使用 `gcc` 命令编译。

```shell
$ gcc -o testGCC add.c div.c main.c mult.c sub.c
$ tree
.
├── add.c
├── build
├── div.c
├── head.h
├── main.c
├── makefile
├── mult.c
├── sub.c
└── testGCC
$ ./testGCC 
a = 20, b = 12
a + b = 32
a - b = 8
a * b = 240
a / b = 1.666667
```

- 编写 makefile 使用 make 编译。

```makefile
CC = gcc
CFLAGS = -Wall -g
target = calc
# 搜索磁盘的源文件
src=$(wildcard *.c)
# 后缀的替换
obj=$(patsubst %.c, build/%.o, $(src))

$(target) : $(obj)                                                                           
	$(CC) $^ -o $@ $(CFLAGS)

build/%.o : %.c | build
	$(CC) $< -c -o $@ $(CFLAGS)

build :
	mkdir -p build

.PHONY:clean
clean:
	-rm -r build/*.o $(target)
```

```shell
$ make
gcc mult.c -c -o build/mult.o -Wall -g
gcc main.c -c -o build/main.o -Wall -g
gcc add.c -c -o build/add.o -Wall -g
gcc div.c -c -o build/div.o -Wall -g
gcc sub.c -c -o build/sub.o -Wall -g
gcc build/mult.o build/main.o build/add.o build/div.o build/sub.o -o calc -Wall -g
$ tree
.
├── add.c
├── build
│   ├── add.o
│   ├── div.o
│   ├── main.o
│   ├── mult.o
│   └── sub.o
├── calc
├── div.c
├── head.h
├── main.c
├── makefile
├── mult.c
└── sub.c
$ ./calc 
a = 20, b = 12
a + b = 32
a - b = 8
a * b = 240
a / b = 1.666667
```

## 规则

​	Makefile 的框架是由规则构成的，make 命令执行时先在 Makefile 文件中查找各种规则，对各种规则进行解析后运行规则。

```cmd
# 每条规则的语法格式:
target1,target2...: depend1, depend2, ...
	command
	......
	......
```

​	每条规则由三个部分组成分别是 **目标（target）**，**依赖（depend）**和 **命令（command）**。

- **命令（command）**：当前这条规则的动作，一般情况下这个动作就是一个 shell 命令。
  - 例如：通过某个命令编译文件、生成文件、进入目录等。
  - 动作可以是多个，每个命令前必须有一个 Tab 缩进并且独占一行。

- **依赖（depend）**：规则所必须的依赖条件，在规则的命令中可以使用这些依赖。
  - 例如：生成可执行文件的目标文件（`*.o`）可以作为依赖使用。
  - 如果规则的命令中不需要任何依赖，那么规则的依赖可以为空。
  - 当前规则中的依赖可以是其他规则中的目标，这样就形成了规则之间的嵌套。
  - 依赖可以根据要执行的命令的实际需求，指定很多个。
- **目标（target）**：规则中的目标，这个目标和规则中的命令是相对的。
  - 通过执行规则中的命令，可以生成一个和目标同名的文件。
  - 规则中可以有多个命令，因此可以通过这多条命令来生成多个目标，所以目标也可以有很多个。
  - 通过执行规则中的命令，可以只执行一个动作，不生成任何文件，这样的目标被称为 **伪目标**。

```makefile
################# 例1 #################
app:a.c b.c c.c
	gcc a.c b.c c.c -o app

################# 例2 #################
# 有多个目标, 多个依赖, 多个命令
app,app1:a.c b.c c.c d.c
	gcc a.c b.c -o app
	gcc c.c d.c -o app1
	
################# 例3 #################	
# 规则之间的嵌套
app:a.o b.o c.o
	gcc a.o b.o c.o -o app
# a.o 是第一条规则中的依赖
a.o:a.c
	gcc -c a.c
# b.o 是第一条规则中的依赖
b.o:b.c
	gcc -c b.c
# c.o 是第一条规则中的依赖
c.o:c.c
	gcc -c c.c
```

## 工作原理

### 规则的执行

​	makefile 中的某一条规则在需要的时候，就会被其他的规则调用，**直到 makefile 中的第一条规则中的所有依赖全部被生成**，第一条规则中的命令就可以基于这些依赖生成对应的目标，make 的任务也就完成了。

```makefile
# makefile
# 规则之间的嵌套
# 规则1
app:a.o b.o c.o
	gcc a.o b.o c.o -o app
# 规则2
a.o:a.c
	gcc -c a.c
# 规则3
b.o:b.c
	gcc -c b.c
# 规则4
c.o:c.c
	gcc -c c.c
```

​	在这个例子中，如果执行 make 名就会根据这个 makefile 中的 四 条规则编译这三个源文件。在解析第一条规则的时候发现里面的三个依赖都是不存在的，因此规则对应的命令也就不能被执行。当依赖不存在的时候，make 就去查找其他的规则，看哪一条规则是用来生成需要的这个依赖的，找到之后就会执行这条规则的命令。因此，规则2，规则3，规则4 里的命令会相继被执行，当 规则 1 中依赖全部被生成之后，对应的命令也就被执行了，因此规则1 的目标被生成，make 工作结束。

### 文件的时间戳

​	make 命令执行的时候会根据文件的时间戳判断是否执行 makefile 文件中相关规则中的命令。

- 目标是通过依赖生成的，因此在正常情况下，**目标时间戳 > 依赖时间戳**，如果执行 make 命令的时候检测到规则中的目标和依赖满足这个条件，那么规则中的命令就不会被执行。
- 当依赖文件更新了，文件时间戳也会随之被更新，这时候，**目标时间戳 <  某些依赖的时间戳**，在这种情况下目标文件会通过规则中的命令被重新生成。
- 如果规则中的**目标对应的文件根本不存在**，那么规则中的命令肯定会被执行。

### 自动推导

​	使用命令 make 编译拓展名伪 `.c`的 C 语言文件的时候，源文件的编译规则不用明确给出。这是因为 make 进行编译的时候会使用一个默认的编译规则，按照默认规则完成对 `.c` 文件的编译，生成对应的 `.o` 文件。它使用命令 `cc -c` 来编译 `.c` 文件。在 Makefile 中只要给出需要构建的目标文件名（一个 `.o` 文件），make 会自动伪这个 `.o` 文件寻找合适的依赖文件（对应的 `.c` 文件），并且使用默认的命令来构建这个目标文件。

- 目录

```shell
$ tree
.
├── add.c
├── div.c
├── head.h
├── main.c
├── makefile
├── mult.c
└── sub.c
```

- makefile

```makefile
# 这是一个完整的 makefile 文件
calc:add.o  div.o  main.o  mult.o  sub.o
        gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
```

- 通过 make 构建项目

```shell
$ make
cc    -c -o add.o add.c
cc    -c -o div.o div.c
cc    -c -o main.o main.c
cc    -c -o mult.o mult.c
cc    -c -o sub.o sub.c
gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
```

​	我们发现上边的 makefile 文件中只有一条规则，依赖中所有的 `.o` 文件在本地项目中都是不存在的，并且也没有其他的规则用来生成这些依赖文件，这时候 make 会使用内部默认的构造规则先将这些依赖文件生成出来，然后再执行规则中的命令，最后生成目标文件 `calc`。

## 变量

​	使用 Makefile 进行规则定义的时候，为了写起来更加灵活，我们可以在里边使用变量。makefile 中的变量分为三种：**自定义变量**、**预定义变量** 和 **自动变量**。

### 自定义变量

​	用 Makefile 进行规则定义的时候，用户可以定义自己的变量，称为用户自定义变量，makefile 中的变量是没有类型的，直接创建变量然后给其复制即可。

```makefile
# 错误, 只创建了变量名, 没有赋值
变量名 
# 正确, 创建一个变量名并且给其赋值
变量名=变量值
# 变量的使用
$(变量的名字)
# 举例 add.o  div.o  main.o  mult.o  sub.o
# 定义变量并赋值
obj=add.o  div.o  main.o  mult.o  sub.o
# 取变量的值
$(obj)
```

- 举例：

```makefile
# 这是一个规则，普通写法
calc:add.o  div.o  main.o  mult.o  sub.o
        gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
        
# 这是一个规则，里边使用了自定义变量
obj=add.o  div.o  main.o  mult.o  sub.o
target=calc
$(target):$(obj)
        gcc  $(obj) -o $(target)
```

### 预定义变量

​	在 Makefile 中有一些已经定义的变量，用户可以直接使用这些变量，不用进行定义。在进行编译的时候，某些条件下 Makefile 会使用这些预定义变量的值进行编译，这些预定义变量的名字一般都是大写的。经常采用的预定义变量如下表：

|  变量名  |            含义            |  默认值  |
| :------: | :------------------------: | :------: |
|    AR    |  生成静态库文件的程序名称  |    ar    |
|    AS    |      汇编编译器的名称      |    as    |
|    CC    |     C 语言编译器的名称     |    cc    |
|   CPP    |    C 语言预编译器的名称    | $(CC) -E |
|   CXX    |    C++ 语言编译器的名称    |   g++    |
|    RM    |     删除文件程序的名称     |  rm -f   |
| ARFLAGS  | 生成静态库库文件程序的选项 |          |
| ASFLAGS  |  汇编语言编译器的编译选项  |          |
|  CFLAGS  |   C 语言编译器的编译选项   |          |
| CPPFLAGS |   C 语言预编译的编译选项   |          |
| CXXFLAGS |  C++ 语言编译器的编译选项  |          |

```makefile
# 这是一个规则，普通写法
calc:add.o  div.o  main.o  mult.o  sub.o
        gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
        
# 这是一个规则，里边使用了自定义变量和预定义变量
obj=add.o  div.o  main.o  mult.o  sub.o
target=calc
CFLAGS=-O3 # 代码优化
$(target):$(obj)
        $(CC)  $(obj) -o $(target) $(CFLAGS)
```

### 自动变量

​	Makefile 中的变量除了用户自定义变量和预定义变量之外，还有一类自动变量。Makefile 中的规则语句中经常会出现目标文件和依赖文件，自动变量用来代表这些规则中的目标文件和依赖文件，并且它们**只能在规则的命令中使用**。

​	下表是一些常见的自动变量：

| 变量 |                             含义                             |
| :--: | :----------------------------------------------------------: |
| `$*` |         表示目标文件的名称，不包含目标文件的拓展名。         |
| `$+` | 表示所有的依赖文件，这些依赖文件之间以空格分开，按照出现的先后为顺序，其中可能包含重复的依赖文件。 |
| `$<` |            **表示依赖项中第一个依赖文件的名称。**            |
| `$?` | 依赖项中，所有比目标文件时间戳晚的依赖文件，依赖文件之间以空格分开。 |
| `$@` |           **表示目标文件的名称，包含文件拓展名。**           |
| `$^` | **依赖项中，所有不重复的依赖文件，这些文件之间以空格分开。** |

```makefile
# 这是一个规则，普通写法
calc:add.o  div.o  main.o  mult.o  sub.o
        gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
        
# 这是一个规则，里边使用了自定义变量
# 使用自动变量, 替换相关的内容
calc:add.o  div.o  main.o  mult.o  sub.o
	gcc $^ -o $@ 			# 自动变量只能在规则的命令中使用
```

## 模式匹配

```makefile
calc:add.o  div.o  main.o  mult.o  sub.o
        gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
# 语法格式重复的规则, 将 .c -> .o, 使用的命令都是一样的 gcc *.c -c
add.o:add.c
        gcc add.c -c

div.o:div.c
        gcc div.c -c

main.o:main.c
        gcc main.c -c

sub.o:sub.c
        gcc sub.c -c

mult.o:mult.c
        gcc mult.c -c
```

​	通过上述例子能够发现从第二个规则开始到第六个规则做的是相同的事情，但是由于文件名不同不得不再文件中写出多个规则，这就让 makefile 文件看起来非常的冗余，同时可读性也会有所下降。

```makefile
# 模式匹配 -> 通过一个公式, 代表若干个满足条件的规则
# 依赖有一个, 后缀为.c, 生成的目标是一个 .o 的文件, % 是一个通配符, 匹配的是文件名
%.o:%.c
	gcc $< -c
```

## 函数

### wildcard

​	`wildcard` 函数的主要作用是获取指定目录下指定类型的文件名，其返回值是**以空格分隔的、指定目录下的所有符合条件的文件名列表**。

```makefile
# 该函数的参数只有一个, 但是这个参数可以分成若干个部分, 通过空格间隔
$(wildcard PATTERN...)
	参数:	指定某个目录, 搜索这个路径下指定类型的文件，比如： *.c
```

- 参数功能
  - PATTERN 指的是某个或多个目录下的对应的某种类型的文件，比如 当前目录下的 `.c` 文件可以写成 `*.c`。
  - 可以指定多个目录，每个路径直接使用空格分隔。
- 返回值
  - 得到的若干个文件的列表，文件名之间使用空格间隔。
  - 示例：`$(wildcard *.c ./sub/*.c)`
    - 返回值格式：`a.c` `b.c` `c.c` `d.c` `e.c` `f.c` `./sub/aa.c` `./sub/bb.c`

```makefile
# 使用举例: 分别搜索三个不同目录下的 .c 格式的源文件
src = $(wildcard /home/robin/a/*.c /home/robin/b/*.c *.c)  # *.c == ./*.c
# 返回值: 得到一个大的字符串, 里边有若干个满足条件的文件名, 文件名之间使用空格间隔
/home/robin/a/a.c /home/robin/a/b.c /home/robin/b/c.c /home/robin/b/d.c e.c f.c
```

### patsubst

​	`patsubst` 函数的功能是按照指定的模式替换指定的文件名的后缀。

```makefile
# 有三个参数, 参数之间使用 逗号间隔
$(patsubst <pattern>,<replacement>,<text>)
```

- 参数功能
  - pattern：这是一个模式字符串，需要指定出要被替换的文件名中的后缀是什么。
    - 文件名和路径不需要关心，因此使用 % 表示即可。
    - 在通配符后边指定出要被替换的后缀，比如：`%.c` 表示 `.c` 的后缀要被替换掉。
  - replacement：这是个模式字符串，指定参数 pattern 中的后缀最终要被替换成什么。
    - 还是使用 % 来表示参数 pattern 中文件的路径和名字。
    - 在通配符 % 后边制定出新的后缀名，比如：`%.o` 这表示原来的后缀被替换为 `.o`。
  - text：该参数中存储这要被替换的原始数据
  - 返回值：
    - 函数返回被替换后的字符串。

```makefile
src = a.cpp b.cpp c.cpp e.cpp
# 把变量 src 中的所有文件名的后缀从 .cpp 替换为 .o
obj = $(patsubst %.cpp, %.o, $(src)) 
# obj 的值为: a.o b.o c.o e.o
```

## makefile 编写

### 目录

```makefile
# 项目目录结构
.
├── add.c
├── div.c
├── head.h
├── main.c
├── mult.c
└── sub.c
# 需要编写makefile对该项目进行自动化编译
```

### 版本 1

```makefile
calc:add.c  div.c  main.c  mult.c  sub.c
        gcc add.c  div.c  main.c  mult.c  sub.c -o calc
```

### 版本 2

```makefile
# 默认所有的依赖都不存在, 需要使用其他规则生成这些依赖
# 因为 add.o 被更新, 需要使用最新的依赖, 生成最新的目标
calc:add.o  div.o  main.o  mult.o  sub.o
        gcc  add.o  div.o  main.o  mult.o  sub.o -o calc

# 如果修改了add.c, add.o 被重新生成
add.o:add.c
        gcc add.c -c

div.o:div.c
        gcc div.c -c

main.o:main.c
        gcc main.c -c

sub.o:sub.c
        gcc sub.c -c

mult.o:mult.c
        gcc mult.c -c
```

### 版本 3

```makefile
# 添加自定义变量 -> makefile中注释前 使用 # 
obj=add.o  div.o  main.o  mult.o  sub.o
target=calc

$(target):$(obj)
        gcc $(obj)  -o $(target)

%.o:%.c
        gcc $< -c
```

### 版本 4

```makefile
# 添加自定义变量 -> makefile中注释前 使用 # 
# 使用函数搜索当前目录下的源文件 .c
src=$(wildcard *.c)
# 将源文件的后缀替换为 .o
# % 匹配的内容是不能被替换的, 需要替换的是第一个参数中的后缀, 替换为第二个参数中指定的后缀
# obj=$(patsubst %.cpp, %.o, $(src)) 将src中的关键字 .cpp 替换为 .o
obj=$(patsubst %.c, %.o, $(src))
target=calc

$(target):$(obj)
        gcc $(obj)  -o $(target)
        
%.o:%.c
        gcc $< -c
```

### 版本 5

```makefile
# 添加自定义变量 -> makefile中注释前 使用 # 
# 使用函数搜索当前目录下的源文件 .c
src=$(wildcard *.c)
# 将源文件的后缀替换为 .o
obj=$(patsubst %.c, %.o, $(src))
target=calc
# obj 的值 xxx.o xxx.o xxx.o xx.o
$(target):$(obj)
        gcc $(obj)  -o $(target)

%.o:%.c
        gcc $< -c

# 添加规则, 删除生成文件 *.o 可执行程序
# 这个规则比较特殊, clean根本不会生成, 这是一个伪目标
clean:
        rm $(obj) $(target)
```

### 最终版

```makefile
# 添加自定义变量 -> makefile中注释前 使用 # 
# 使用函数搜索当前目录下的源文件 .c
src=$(wildcard *.c)
# 将源文件的后缀替换为 .o
obj=$(patsubst %.c, %.o, $(src))
target=calc

$(target):$(obj)
        gcc $(obj)  -o $(target)

%.o:%.c
        gcc $< -c

# 添加规则, 删除生成文件 *.o 可执行程序
# 声明clean为伪文件
.PHONY:clean
clean:
        # shell命令前的 - 表示强制这个指令执行, 如果执行失败也不会终止
        -rm $(obj) $(target) 
```

### 小型项目 makefile 框架

```shell
# 目录结构
.
├── build
├── include
│   └── head.h	==> 头文件, 声明了加减乘除四个函数
├── main.c		==> 测试程序, 调用了head.h中的函数
└── src
    ├── add.c	==> 加法运算
    ├── div.c	==> 除法运算
    ├── mult.c  ==> 乘法运算
    └── sub.c   ==> 减法运算
```

```makefile
CC = gcc
CFLAGS = -Wall
LDFLAGS =
PREFIX = /usr/local
BINDIR = $(PREFIX)/bin
INCLUDEDIR = $(PREFIX)/include
LIBDIR = $(PREFIX)/lib

# 构建目标
TARGET = app

# 源文件和目标文件
SRC = $(wildcard *.c src/*.c)
OBJ = $(patsubst %.c, build/%.o, $(notdir $(SRC)))

# 包含头文件的路径
INCLUDE = ./include

$(TARGET) : $(OBJ)
	$(CC) -o bin/$@ $^ $(LDFLAGS) 
	
build/%.o : src/%.c | build
	$(CC) -o $@ $< -I $(INCLUDE) -c $(CFLAGS)

build/main.o : main.c | build
	$(CC) -o $@ $< -I $(INCLUDE) -c $(CFLAGS)

build :
	mkdir -p ./build
	mkdir -p ./bin                                                                                                                                                                     
.PHONY : clean all

clean : 
	-rm -rf build/* bin/*

all : $(TARGET)

# 自定义帮助信息
help :
	@echo "Usage:"
	@echo "  make [target]"
    @echo ""
	@echo "Targets:"
	@echo "  all         Build the target."
	@echo "  clean       Remove all generated files."
	@echo "  help        Show this help message.
```



