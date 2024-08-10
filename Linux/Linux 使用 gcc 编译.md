## 安装 gcc 编译器

```shell
sudo apt install gcc
```

## 查看 gcc 版本

```shell
gcc -v
```

## 常用选项

### -o 选项：

​	可以指定编译后输出的可执行文件的名称。如：`hellworld.c` 编译后指定输出文件名为 `test`，使用方式如下：

```shell
gcc -o test helloworld.c
```

### -c 选项：

​	只编译 C 语言代码，不进行汇编和链接。

```shell
gcc -c helloworld.c
```

​	这样会产生一个叫 `helloworld.o` 的目标文件。

### -S 选项：

​	编译并产生汇编源文件。

```shell
gcc -S helloworld.c
```

​	这样会产生一个 `helloworld.s` 的汇编源文件。

### -E 选项：

​	只对 C 源文件进行预处理。

```shell
gcc -E helloworld.c
```

​	只会对 C 源文件中的宏和预处理进行展开，不编译 C 源文件。