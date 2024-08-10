## main() 函数参数

​	在 C 语言中，main 函数可以带参数。main函数原型通常为以下两种形式之一

```c++
int main(void)
int main(int argc, char* argv[])
```

- `int argc`：参数数量，`argc`是一个整数，表示传递给程序的命令行参数的数量。这个数量包括程序本身名字，所以 `argc` 的最小值为 1。
- `char* argv[]`：参数值，`argv` 是一个字符指针宿主，其中每个元素指向一个命令行参数的字符串。`argv[0]` 总是程序的名称或路径，argv[1] 到 `argv[argc - 1]`是用户传递的实际参数。

​	这两个参数允许程序接收来自命令行的输入，而从根据不同的参数执行不同的操作。 

## 基本用法

​	假设我们有一个程序 `myprogram`，并在命令行中执行它：

```shell
./myprogram arg1 arg2
```

​	此时，`argc` 的值为3，因为程序名和两个参数总共有 3 个。`argv` 数组包含以下内容：

```shell
argv[0]: "./myprogram"
argv[1]: "arg1"
argv[2]: "arg2"
```

​	在程序内部，我们可以遍历 `argv` 数组，访问并处理这些参数。

```c++
#include <iostream>

int main(int argc, char* argv[])
{
	std::cout << "Number of arguments: " << argc << std::endl;
	
	for(int i = 0; i < argc; ++i)
	{
		std::cout << "Argument " << i << ": " << argv[i] << std::endl;
	}
	
	return 0;
}
```

​	在上述代码中，程序会输出参数的数量和每个参数的内容。这个简单的例子展示了如何获取并打印命令行参数，但在实际应用中，着各种机制远比打印参数要复杂得多。

## 实际应用

### 构建灵活的命令行工具

​	命令行工具往往需要处理各种输入，以执行不同的操作。`int argc`，`char* argv[]`使我们能够轻松实现这一目标。例如，一个简单的文件复制工具可能需要用户输入源文件路径和目标路径。

```shell
./copyfile source.txt destination.txt
```

​	在程序的内部，我们可以检查 `argc` 是否为 3，并使用 `argv[1]` 和 `argv[2]` 来获取源文件和目标文件的路径，从而实现文件复制功能。这种设计使得工具使用起来直观且高效。

### 支持可选参数和标志

​	通过检查 `argc` 和解析 `argv` 中的内容，我们可以设计支持可选参数的程序。例如，一个压缩工具可能允许用户指定压缩级别。

```shell
./compressfile input.txt -level 5
```

​	在这种情况下，程序可以检查 `argv` 数组中的 -level 标志，并读取其后的数字值，以确定压缩级别。通过这种方式，我们可以让用户对程序的行为进行精细控制。