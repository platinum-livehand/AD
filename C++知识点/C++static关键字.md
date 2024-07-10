## static

​	在 C++ 中，static 关键字有多种用途，可以用于变量和函数，作用范围包括**局部变量**、**全局变量**、类的**成员变量**(**属性**)和**成员函数**(**方法**)。

![](..\picture\static关键字.png)

## 局部静态变量

​	静态局部变量在函数调用结束后并不会被销毁，而是保持其值，知道程序结束。静态局部变量在函数首次调用时初始化，并且此后每次调用该函数时都会保持上一次的值。

```c++
#include <iostream>

void countCalls() {
    static int count = 0;  // 静态局部变量
    count++;
    std::cout << "Function called " << count << " times" << std::endl;
}

int main() {
    countCalls();  // 输出: Function called 1 times
    countCalls();  // 输出: Function called 2 times
    countCalls();  // 输出: Function called 3 times
    return 0;
}
```

## 静态全局变量

​	静态全局变量的作用域仅限于定义它的文件。与普通全局变量不同，静态全局变量不能在其它文件中使用。

```c++
// file.cpp
#include <iostream>

static int globalVar = 42;  // 静态全局变量

void printGlobalVar() {
    std::cout << "Global variable: " << globalVar << std::endl;
}
```

```c++
// main.cpp
// extern int globalVar;
extern void printGlobalVar();

int main() {
    printGlobalVar();  // 输出: Global variable: 42
    return 0;
}
```

## 类的静态成员变量(属性)和成员函数(方法)

​	类的静态成员变量和函数属于类，而不是某个对象。静态成员变量和函数在类的所有实例之间共享。**类的静态成员函数可以访问静态成员变量，但不能访问非静态成员变量，因为它们不依赖于类的具体实例。**

```c++
// MyClass.h
#ifndef MYCLASS_H
#define MYCLASS_H

class MyClass {
public:
    static int staticVar;  // 声明静态成员变量
    static void printStaticVar();  // 声明静态成员函数
};

#endif // MYCLASS_H
```

```c++
// MyClass.cpp
#include <iostream>
#include "MyClass.h"

int MyClass::staticVar = 0;  // 定义静态成员变量

void MyClass::printStaticVar() {
    std::cout << "Static variable: " << staticVar << std::endl;
}
```

```C++
// main.cpp
#include "MyClass.h"

int main() {
    MyClass::staticVar = 10;
    MyClass::printStaticVar();  // 输出: Static variable: 10
    MyClass::staticVar = 20;
    MyClass::printStaticVar();  // 输出: Static variable: 20
    return 0;
}
```

