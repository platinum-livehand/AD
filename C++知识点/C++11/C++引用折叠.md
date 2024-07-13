## 引用折叠

​	引用折叠(Reference Collapsing) 是 C++11 中引入的一项规则，用于处理引用的引用。引用折叠的规则确定了在多级引用(尤其是在模板编程中) 情况下，最终结果的引用类型。这在处理模板参数时尤其重要，因为模板参数可能会涉及左值引用和右值引用的组合。

## 引用折叠的规则

1. `T& &`变为 `T&`。
2. `T& &&`变为 `T&`。
3. `T&& &`变为 `T&`。
4. `T&& &&`变为 `T&`。

## 应用场景

​	引用折叠在模板编程中最为常见，特别是当使用万能引用(Universal Reference) 时。万能引用可以根据实参的类型，变成左值引用或右值引用。

```c++
#include <iostream>
#include <type_traits>

template <typename T>
void process(T&& param) {
    // 使用 std::is_same 检查最终的类型
    if (std::is_same<T&&, int&>::value) {
        std::cout << "T&& is int&" << std::endl;
    } else if (std::is_same<T&&, int&&>::value) {
        std::cout << "T&& is int&&" << std::endl;
    } else {
        std::cout << "Unknown type" << std::endl;
    }
}

int main() {
    int x = 42;
    const int cx = x;

    process(x);          // T 被推导为 int&，param 是 int& && -> int& 类型
    process(cx);         // T 被推导为 const int&，param 是 const int& && -> const int& 类型
    process(42);         // T 被推导为 int，param 是 int&& -> int&& 类型
    process(std::move(x)); // T 被推导为 int&&，param 是 int&& && -> int&& 类型

    return 0;
}
```

```shell
T&& is int&
Unknown type
T&& is int&&
T&& is int&&
```

## 完美转发

​	引用折叠的一个重要应用是完美转发。完美转发使用 `std::forward`来保留参数中的左右值性质。

```c++
#include <iostream>
#include <utility>

void process(int& x) {
    std::cout << "Lvalue reference" << std::endl;
}

void process(int&& x) {
    std::cout << "Rvalue reference" << std::endl;
}

template <typename T>
void forwarder(T&& param) {
    process(std::forward<T>(param));  // 保留 param 的值类别
}

int main() {
    int x = 42;

    forwarder(x);          // 调用 Lvalue reference 版本的 process
    forwarder(42);         // 调用 Rvalue reference 版本的 process
    forwarder(std::move(x)); // 调用 Rvalue reference 版本的 process

    return 0;
}
```

```shell
Lvalue reference
Rvalue reference
Rvalue reference
```

## 总结

- **引用折叠**规则在处理多级引用时确定最终的引用类型。
- **完美转发**使用引用折叠和 `std::forward` 来保留参数的左右值属性。
- **万能引用**通过引用折叠实现了对左值和右值的正确处理。