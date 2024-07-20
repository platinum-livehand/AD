## C++ std::bind

​	`std::bind` 是 C++11 标准引入的一个函数模板，它的主要作用是**将一个函数(或函数对象)的某些参数进行绑定，生成一个新的函数对象**。这个新的函数对象可以暂时保存一些参数的值，并在将来调用时使用这些参数。

### 绑定普通函数

```c++
#include <iostream>
#include <functional>

void print_sum(int a, int b) {
    std::cout << a + b << std::endl;
}

int main() {
    // 创建一个新的函数对象，将第一个参数绑定为2
    auto bound_function = std::bind(print_sum, 2, std::placeholders::_1);
    bound_function(3); // 输出 5

    return 0;
}
```

​	在这个示例中，`std::bind` 将 `print_sum` 的第一个参数绑定为 2，并留下第二个参数以供将来提供。

### 绑定成员函数

​	绑定成员函数时，需要提供对象实例。

```c++
#include <iostream>
#include <functional>

class Printer {
public:
    void print(int a) const {
        std::cout << a << std::endl;
    }
};

int main() {
    Printer printer;
    // 创建一个新的函数对象，将成员函数绑定到对象实例
    auto bound_member_function = std::bind(&Printer::print, &printer, std::placeholders::_1);
    bound_member_function(5); // 输出 5

    return 0;
}
```

​	在这个示例中，std::bind 将 Printer 类的成员函数 print 绑定到对象实例 printer，并留下参数以供将来提供。

### 绑定函数对象

```c++
#include <iostream>
#include <functional>

struct Adder {
    int operator()(int a, int b) const {
        return a + b;
    }
};

int main() {
    Adder adder;
    // 创建一个新的函数对象，将第一个参数绑定为3
    auto bound_function_object = std::bind(adder, 3, std::placeholders::_1);
    std::cout << bound_function_object(4) << std::endl; // 输出 7

    return 0;
}
```

​	在这个示例中，`std::bind` 将 `Adder` 函数对象的第一个参数绑定为 3 ，并留下第二个参数以供将来提供。

## C++ std::placeholders

​	C++ 标准库中的 `std::placeholders` 命名空间定义了一组占位符，主要用于 `std::bind`。这些占位符包括_1、_2、3等，分别表示绑定函数的第一个、第二个、第三个参数，以此类推。占位符允许我们在绑定时灵活地指定哪些参数由调用时传入，哪些参数提前绑定。

```c++
#include <iostream>
#include <functional>

void multiply(int a, int b, int c) {
    std::cout << "Result: " << a * b * c << std::endl;
}

int main() {
    auto bindMultiply = std::bind(multiply, std::placeholders::_1, 10, std::placeholders::_2);
    bindMultiply(2, 3); // 输出: Result: 2 * 10 * 3 = 60
    return 0;
}
```

​	这个例子中，`multiply` 函数的第一个和第三个参数由调用时传入，而第二个参数绑定为10。

## 高级用法：嵌套 `std::bind`

```c++
#include <iostream>
#include <functional>

void complexFunction(int a, int b, int c, int d) {
    std::cout << "Result: " << (a + b) * (c - d) << std::endl;
}

int main() {
    auto bindFirstHalf = std::bind(complexFunction, std::placeholders::_1, std::placeholders::_2, 10, 5);
    auto bindSecondHalf = std::bind(bindFirstHalf, 2, 3);
    bindSecondHalf(); // 输出: Result: (2 + 3) * (10 - 5) = 25
    return 0;
}
```

​	在这个例子中，我们首先使用 `std::bind` 将 `complexFunction` 的第三个和第四个参数绑定为 10 和 5 ，然后再次使用 `std::bind` 将前两个参数绑定为 2 和 3。

## 使用 lambda 表达式与 std::bind

​	很多情况下我们可以使用 lambda 表达式代替 `std::bind`。

```c++
#include <iostream>
#include <functional>

void display(int a, int b) {
    std::cout << "a: " << a << ", b: " << b << std::endl;
}

int main() {
    auto lambdaDisplay = [](int a) { display(a, 20); };
    lambdaDisplay(10); // 输出: a: 10, b: 20
    return 0;
}
```

## 应用场景

### 事件处理器

```c++
#include <iostream>
#include <functional>

class Button {
public:
    std::function<void()> onClick;

    void click() {
        if (onClick) {
            onClick();
        }
    }
};

class Application {
public:
    void run() {
        Button button;
        button.onClick = std::bind(&Application::handleClick, this, 42);
        button.click();
    }

    void handleClick(int data) {
        std::cout << "Button clicked with data: " << data << std::endl;
    }
};

int main() {
    Application app;
    app.run(); // 输出: Button clicked with data: 42
    return 0;
}
```

​	在这个例子中，我们使用 `std::bind` 将 Application 类的成员函数与特定的**函数包装器**绑定在一起，并设置为按钮的点击处理器。