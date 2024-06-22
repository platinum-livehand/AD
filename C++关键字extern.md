​	在C++中，关键字`extern`用于声明变量或函数的外部链接性（external linkage）。外部链接性表示该变量或函数可以被其他文件中的代码所访问和使用。

具体来说，`extern`关键字有两种常见的用法：

声明外部变量： 当在一个文件中使用外部定义的全局变量时，需要使用`extern`关键字进行声明。这告诉编译器该变量是在其他文件中定义的，并且需要在链接时找到其定义。

```c++
// File1.cpp
int globalVariable = 10; // 定义全局变量

// File2.cpp
extern int globalVariable; // 声明全局变量，表示该变量是在其他文件中定义的
```

在头文件中声明变量时，使用`extern`关键字可以确保该变量的定义不会在每个包含该头文件的源文件中重复出现。

```c++
// MyHeader.h
extern int globalVariable; // 在头文件中声明全局变量

// File1.cpp
#include "MyHeader.h"
// 可以使用 globalVariable

// File2.cpp
#include "MyHeader.h"
// 可以使用 globalVariable
```

声明外部函数： 当在一个文件中使用其他文件中定义的函数时，同样需要使用`extern`关键字进行声明。

```c++
// main.c
extern void someFunction(); // 使用extern声明函数

int main() {
    someFunction(); // 调用在另一个文件中定义的函数
    return 0;
}
```

