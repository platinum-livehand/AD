​	`std::forward`是 C++11 引入的一个模板函数，用于实现**完美转发**(perfect forwarding)。它主要在泛型编程和模板中使用，以便将参数原样地传递给另一个函数，保持其左值或右值属性。完美转发允许编写更通用和高效的代码。

## 基本概念

​	在 C++ 中，函数参数有**左值引用**(lvalue reference)和**右值引用**(rvalue reference)两种类型。左值引用表示对一个对象的引用，而右值引用表示对一个临时对象的引用。`std::forward`可以根据参数的原始类型将其完美转发，即保持其左值或右值属性。

## 头文件

```c++
#include <utility>
```

## 语法

```c++
template <typename T>
T&& std::forward(typename std::remove_reference<T>::type& arg) noexcept;
```

## 基本使用

```c++
#include <iostream>
#include <utility>

void process(int& x) {
    std::cout << "Lvalue reference: " << x << std::endl;
}

void process(int&& x) {
    std::cout << "Rvalue reference: " << x << std::endl;
}

// 模板函数，使用 T&& 万能引用，获取调用者的值类型和引用类型
template <typename T>
void forward_example(T&& arg) {
    process(std::forward<T>(arg));
}

int main() {
    int a = 10;
    forward_example(a);           // 传递左值
    forward_example(20);          // 传递右值

    return 0;
}
```

输出：

```powershell
PS D:\C++Project\test\bin> ."D:/C++Project/test/bin/cmake_test.exe"
Lvalue reference: 10
Rvalue reference: 20
```

## 完美转发工厂函数

```c++
#include <iostream>
#include <memory>
#include <utility>

class MyClass {
public:
    MyClass(int x) { std::cout << "MyClass constructor: " << x << std::endl; }
};

template <typename T, typename... Args>
std::unique_ptr<T> make_unique_forward(Args&&... args) {
    return std::unique_ptr<T>(new T(std::forward<Args>(args)...));
}

int main() {
    auto my_obj = make_unique_forward<MyClass>(42);
    return 0;
}
```

输出：

```powershell
PS D:\C++Project\test\bin> ."D:/C++Project/test/bin/cmake_test.exe"
MyClass constructor: 42
```

## 完美可变参数模板

```c++
#include <iostream>
#include <utility>

void print() {
    std::cout << "End of variadic template." << std::endl;
}

template <typename T, typename... Args>
void print(T&& first, Args&&... args) {
    std::cout << "Argument: " << first << std::endl;
    print(std::forward<Args>(args)...);
}

int main() {
    print(1, 2.5, "hello");
    return 0;
}
```

输出：

```powershell
PS D:\C++Project\test\bin> ."D:/C++Project/test/bin/cmake_test.exe"
Argument: 1
Argument: 2.5
Argument: hello
End of variadic template.
```

