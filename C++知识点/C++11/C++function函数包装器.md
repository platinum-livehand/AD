## 概述

​	类模板 `std::function`是通用多态函数包装器。存储的可调用对象被称为 `std::function` 的目标。若 `std::function` 不含目标，则称它为空。调用空的`std::function` 的目标导致抛出 `std::bad_function_call` 异常。

std::function可包装下列可调用元素类型：

- 函数
- 函数指针
- 类成员函数(也包括模板类)
- 任意类型的函数可调用对象（比如重载了`operator()`操作符并且拥有函数闭包的函数体对象）
- lambda表达式

## 拷贝、移动

```c++
#include <iostream>
#include <functional>

int fun1(int a) {return a;}

int main() {
    std::cout << "Hello std::function" << std::endl;

    //拷贝赋值运算符
    std::function<int(int)> fc1 = fun1; 
    std::cout << fc1(1) << std::endl;

    //移动赋值运算符
    std::function<int(int)>&& fc2 = std::move(fun1); 
    std::cout << fc2(2) << std::endl;

    //拷贝构造
    std::function<int(int)> fc3(fun1); 
    std::cout << fc3(3) << std::endl;

    //移动构造
    std::function<int(int)> fc4(std::move(fun1)); 
    std::cout << fc4(4) << std::endl;

    return 0;
}
```

## 包装模板类成员函数、静态函数、对象函数

```c++
#include <functional>
#include <iostream>

template <typename T>
struct Test {
    T operator()(T a) { return a * a; }
    T Square(T a) { return a * a; }
    static T Square2(T a) { return a * a; }
};

int main(int argc, char *argv[]) {
    // 模板类成员函数
    Test<int> test;
    std::function<int(int)> fc =
        std::bind(&Test<int>::Square, test, std::placeholders::_1);
    std::cout << fc(7) << std::endl;

    // 模板类静态函数
    std::function<int(int)> fc2 = Test<int>::Square2;
    std::cout << fc2(8) << std::endl;

    // 模板类对象函数(仿函数)
    std::function<int(int)> fc3 = Test<int>();
    std::cout << fc3(9) << std::endl;

    return 0;
}
```

## 包装类成员函数、静态函数

```c++
#include <functional>
#include <iostream>

struct Test {
    int Square(int a) { return a * a; }
    static int Square2(int a) { return a * a; }    
};

int main(int argc, char *argv[]) {
    // 类成员函数
    Test test;
    std::function<int(int)> fc =
        std::bind(&Test::Square, test, std::placeholders::_1);
    std::cout << fc(6) << std::endl;

    // 类静态函数
    std::function<int(int)> fc2 = Test::Square2;
    std::cout << fc2(7) << std::endl;
    
    return 0;
}
```

​	这里我们用到了`std::bind`，C++11中`std::bind`函数的意义就如字面上的意思一样，用来绑定函数调用的某些参数。`std::bind`的思想其实是一种延迟计算的思想，将可调用对象保存起来，然后在需要的时候再调用。而且这种绑定是非常灵活的，不论是普通函数还是函数对象还是成员函数都可以绑定，而且其参数可以支持占位符。这里的`std::placeholders::_1`是一个占位符，且绑定第一个参数，若可调用实体有2个形参，那么绑定第二个参数的占位符是`std::placeholders::_2`。

## 包装普通模板函数、函数对象

```c++
#include <iostream>
#include <functional>

struct Add{
    int operator()(int x, int y) {
        return x + y;
    }
};

template<typename T>
T fun1(T a) {return a;}

int main() {

    // 普通函数对象
    std::function<int(int, int)> fc = Add(); 
    std::cout << fc(30, 12) << std::endl;

    // 普通模板函数
    std::function<int(int)> fc2 = fun1<int>;
    std::cout << fc2(42) << std::endl; 

    return 0;
}
```

## 包装普通函数、函数指针、lambda表达式

```c++
#include <iostream>
#include <functional>

int fun1(int a) {
    return a;
}

int (*fun_ptr)(int) = fun1; // 初始化函数指针

int main() {
    // 普通函数
    std::function<int(int)> fc1 = fun1;
    std::cout << fc1(1) << std::endl;

    // 函数指针
    std::function<int(int)> fc2 = fun_ptr;
    std::cout << fc2(2) << std::endl;

    // lambda表达式
    auto square = [](int a) { return a * a; };
    std::function<int(int)> fc3 = square;
    std::cout << fc3(3) << std::endl;

    return 0;
}
```

## 函数形参

```c++
#include <functional>
#include <iostream>

void callback(int x, const std::function<void(int)>& f) {
    if (!(x & 1)) {
        f(x);
    }
}

void output(int x) { std::cout << x << " "; }

int main(void) {
    for (int i = 0; i < 10; ++i) {
        callback(i, output);
    }
}
```

## 总结

​	从上面的例子可以看出，`std::function`可以应用的范围很广，而且没有模板带来的头文件膨胀问题，非常适合取代函数指针。然而，`std::function`相较于函数指针，性能上会有一点点损失，如果不是在性能特别关键的场合，还是大胆拥抱C++ 11这一新特性吧！

