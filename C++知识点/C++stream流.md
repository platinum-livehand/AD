## 流

​	在 C++ 中，流（stream）是一种用于处理输入和输出的抽象概念。流将数据从一个地方传输到另一个地方，可以是从键盘到内存，或者从内存到显示器，甚至从一个文件到另一个文件。流提供了一个一致的接口，简化了输入和输出操作。理解流的概念和它们在C++中的使用对于处理文件和用户输入输出是非常重要的。

![](..\picture\C++ stream-1.png)

## 流的基本概念

### 1.输入流 (`istream`) 和输出流 (`ostream`)

- 输入流用于从外部设备(如键盘、文件等)读取数据。
- 输出流用于向外部设备(如显示器、文件等)写入数据。

### 2.标准输入输出流

- `std::cin`：标准输入流对象，用于从键盘读取数据。
- `std::cout`：标准输出流对象，用于向显示器输出数据。
- `std::cerr`：标准错误流对象，用于输出错误信息到显示器，通常不进行缓冲。
- `std::clog`：标准日志流对象，用于输出日志信息到显示器，进行缓冲。

## 流的分类

### 1.字符流和二进制流

- 字符流：处理字符数据，通常用于文本文件的输入输出。
- 二进制流：处理二进制数据，通常用于二进制文件的输入输出。

### 2.文件流

- `ifstream`：文件输入流，用于从文件读取数据。
- `ofstream`：文件输出流，用于向文件写入数据。
- `fstream`：文件读写流，用于同时读取和写入文件。

## 流的使用

​	使用流进行输入输出非常直观。例如，使用标准输出流`std::cout`输出文本到控制台。

```c++
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

​	在这个例子中，`std::cout`是一个输出流对象，`<<`运算符用于向流中插入数据，`std::endl`用于在输出结束时换行并刷新缓冲区。

## 文件流的例子

​	文件流用于文件的输入输出操作。以下时一个读取文件内容并将其输出到控制台的例子。

```c++
#include <iostream>
#include <fstream>
#include <string>

int main() {
    std::ifstream inputFile("../README.md"); // 打开文件进行读取
    if (!inputFile) {
        std::cerr << "无法打开文件" << std::endl;
        return 1;
    }

    std::string line;
    while (std::getline(inputFile, line)) { // 从文件中读取每一行
        std::cout << line << std::endl; // 输出到控制台
    }

    inputFile.close(); // 关闭文件
    return 0;
}
```

```shell
qy@localhost:~/cmakelist_test/bin$ /home/qy/cmakelist_test/bin/cmakelist_test
test c++ stream input to console
```

​	在这个例子中，`std::ifstream` 用于从文件 `README.md` 中读取数据。`std::getline`函数从文件流中逐行读取内容，并输出到`std::cout`。

## 流的操作

​	流支持各种操作符和方法，例如

- `<<`和`>>`：插入和提取运算符，用于输出和输入数据。
- `getline()`：从流中读取一行数据。
- `open()`和`close()`：用于打开和关闭文件流。
- `eof()`：检查是否到达流的末尾。
- `fail()`：检查流的状态是否失败。

## 总结

​	C++ 中的流提供了一种统一的接口来处理各种输入和输出操作，包括从键盘读取输入、向显示器输出数据、从文件读取数据以及向文件写入数据。理解流的工作原理和如何使用它们是有效进行输入输出操作的关键。通过流，程序员可以简化和标准化这些操作，从而提高代码的可读性和可维护性。