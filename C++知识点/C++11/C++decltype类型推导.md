## `decltype`类型推导

​	`decltype`是 C++11 引入的关键字，用于查询表达式或变量的类型。在许多场景中，特别是模板编程和类型推导中，它非常有用。

## 基本用法

### 查询变量类型

```c++
#include <iostream>

int main() {
    int a = 5;
    decltype(a) b = 10; // b 的类型是 int
    std::cout << "Type of b is int: " << std::boolalpha << std::is_same<decltype(b), int>::value << std::endl;
    return 0;
}
```

### 查询表达式类型

```c++
#include <iostream>

int main() {
    int x = 5;
    int y = 10;
    decltype(x + y) z = 15; // z 的类型是 int
    std::cout << "Type of z is int: " << std::boolalpha << std::is_same<decltype(z), int>::value << std::endl;
    return 0;
}
```

### 结合`auto`使用

```c++
#include <iostream>

auto add(int a, int b) -> decltype(a + b) {
    return a + b;
}

int main() {
    int result = add(5, 10); // result 的类型是 int
    std::cout << "Result of add(5, 10) is: " << result << std::endl;
    return 0;
}
```

​	这里使用了 C++11 的一种新的函数**返回类型声明**方式，这种方式允许我们在参数列表之后指定返回类型。

## 高级用法

### 用于函数返回类型

```c++
#include <iostream>

template <typename T1, typename T2>
auto add(T1 a, T2 b) -> decltype(a + b) {
    return a + b;
}

int main() {
    auto result = add(5, 10.5); // result 的类型是 double
    std::cout << "Result of add(5, 10.5) is: " << result << std::endl;
    return 0;
}
```

​	这个示例中，`decltype` 的使用有助于自动推导和确保函数 `add` 的返回类型，特别是在模板编程中，当参数类型未知或多变时，这种做法非常有用。

### 用于类成员函数的返回类型

```c++
#include <iostream>
#include <vector>

class Example {
public:
    std::vector<int> vec;
    
    decltype(vec.begin()) begin() {
        return vec.begin();
    }
};

int main() {
    Example ex;
    ex.vec = {1, 2, 3};
    auto it = ex.begin(); // it 的类型是 std::vector<int>::iterator
    std::cout << "First element is: " << *it << std::endl;
    return 0;
}
```

### 结合 `decltype` 和 `auto`

C++14 引入了 `decltype(auto)`，用于推导返回类型。

```c++
#include <iostream>

decltype(auto) foo() {
    int x = 10;
    return x; // 返回类型是 int
}

int main() {
    auto result = foo();
    std::cout << "Result of foo() is: " << result << std::endl;
    return 0;
}
```

