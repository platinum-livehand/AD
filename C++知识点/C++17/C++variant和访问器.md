​	C++17引入了 `std::variant`，它是一个类型安全的联合体，可以用来在运行时存储多个类型之一。`std::variant`提供了一个更加现代化和安全的方式来处理多个可能的类型。

## 定义和初始化

​	可以使用 std::variant 定义一个变量，该变量可以存储多种类型中的一种。

```c++
using MyVariant = std::variant<int, double, std::string>;
MyVariant var;
var = 42;
std::cout << "Initial value (int): " << std::get<int>(var) << std::endl;
```

## 访问 std::variant 的值

​	使用 `std::get` 或 `std::get_if`来访问存储的值。

```c++
std::cout << "Initial value (int): " << std::get<int>(var) << std::endl;
```

## 检查存储的类型

​	使用 `std::holds_alternative` 来检查当前存储的类型。

```c++
if (std::holds_alternative<int>(var)) {
    std::cout << "Variant holds an int" << std::endl;
} else if (std::holds_alternative<double>(var)) {
    std::cout << "Variant holds a double" << std::endl;
} else if (std::holds_alternative<std::string>(var)) {
    std::cout << "Variant holds a string" << std::endl;
}
```

## 访问存储类型的索引

​	使用 `index` 方法来获取当前存储类型的索引。

```c++
std::cout << "Current index: " << var.index() << std::endl;
```

## 处理不同类型的值

​	使用 `std::visit` 来访问和处理存储的值

```c++
std::variant<int, double, std::string> v = "hello";

std::visit([](auto&& arg) {
    using T = std::decay_t<decltype(arg)>;
    if constexpr (std::is_same_v<T, int>) {
        std::cout << "int: " << arg << std::endl;
    } else if constexpr (std::is_same_v<T, double>) {
        std::cout << "double: " << arg << std::endl;
    } else {
        static_assert(std::is_same_v<T, std::string>);
        std::cout << "string: " << arg << std::endl;
    }
}, v);
```

## 示例

```c++

#include <iostream>
#include <variant>
#include <string>

// 定义一个 std::variant，可以存储 int、double 或 std::string
using MyVariant = std::variant<int, double, std::string>;

// 定义一个访问者类
struct MyVisitor {
    void operator()(int i) const {
        std::cout << "Visited int: " << i << std::endl;
    }

    void operator()(double d) const {
        std::cout << "Visited double: " << d << std::endl;
    }

    void operator()(const std::string& s) const {
        std::cout << "Visited string: " << s << std::endl;
    }
};

int main() {
    MyVariant var;

    // 初始化为 int
    var = 42;
    std::cout << "Initial value (int): " << std::get<int>(var) << std::endl;

    // 使用 std::visit 访问和处理存储的值
    std::visit(MyVisitor(), var);

    // 重新赋值为 double
    var = 3.14;
    std::cout << "New value (double): " << std::get<double>(var) << std::endl;
    std::visit(MyVisitor(), var);

    // 重新赋值为 std::string
    var = std::string("Hello, world!");
    std::cout << "New value (string): " << std::get<std::string>(var) << std::endl;
    std::visit(MyVisitor(), var);

    return 0;
}
```

