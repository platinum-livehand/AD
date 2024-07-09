## 外部链接性(跨编译单元)

​	在C++中，关键字`extern`用于声明变量或函数的**外部链接性**（external linkage）。外部链接性表示该变量或函数可以被其他文件中的代码所访问和使用。

## 声明外部变量

​	在 C++ 中，`extern` 关键字用于声明在其他文件中定义的变量，这样可以在当前文件中引用该变量。

```c++
// file.cpp
#include <iostream>

// 定义全局变量
int globalVar = 42; 

// 定义全局函数
void printGlobalVar() {
    std::cout << "Global variable: " << globalVar << std::endl;
}
```

```c++
// mian.cpp
#include <iostream>

// 声明在其他文件中定义的全局变量
extern int globalVar;

// 声明在其他文件中定义的全局函数
extern void printGlobalVar();

void modifyGlobalVar() {
    globalVar = 100;
}

int main() {
    printGlobalVar(); // 输出: Global variable: 42
    modifyGlobalVar();
    printGlobalVar(); // 输出: Global variable: 100
    return 0;
}
```

​	通过这种方式，你可以在多个文件之间共享全局变量和函数，而不需要通过 `#include` 指令来包含它们的定义。这种方法特别适用于跨文件共享全局状态的场景。

## 在命名空间中使用`extern`

```c++
// file.cpp
#include <iostream>

namespace MyNamespace {
    int globalVar = 42; // 定义全局变量

    void printGlobalVar() {
        std::cout << "Global variable: " << globalVar << std::endl;
    }
}
```

```c++
// main.cpp
#include <iostream>

// 声明在其他文件中定义的全局变量和函数
namespace MyNamespace {
    extern int globalVar;
    extern void printGlobalVar();
}

void modifyGlobalVar() {
    MyNamespace::globalVar = 100;
}

int main() {
    MyNamespace::printGlobalVar(); // 输出: Global variable: 42
    modifyGlobalVar();
    MyNamespace::printGlobalVar(); // 输出: Global variable: 100
    return 0;
}
```

