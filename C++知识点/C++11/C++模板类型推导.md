## C++ 模板类型推导

​	C++ 的模板类型推导是一个重要的特性，使编译器能够在编译时自动推断模板参数的类型。这种特性极大地提高了代码的灵活性和可读性，使得模板可以在更通用地应用于不同类型的数据。

## 基本概念

​	在 C++ 中，模板参数的类型推导涉及到三种主要类型：`T`、`T&`、`T&&`。

- `T`：按**值传递**，模板参数 `T`会**忽略传递的对象的引用性和常量性**，直接得到基础类型。
- `T&`：按**引用传递**，模板参数 `T` 会**保留传递参数的引用性和常量性**。
- `T&&`：按**右值引用传递**，模板参数 `T`会保留传递对象的右值引用性，支持完美转发

## 值传递

```c++
#include <iostream>
#include <typeinfo>

template <typename T>
void byValue(T param) {
    std::cout << "Type: " << typeid(T).name() << std::endl;
    param = 100;  // 修改形参的值
}

int main() {
    int x = 42;
    const int cx = x;
    int& rx = x;

    byValue(x);   // T 被推导为 int
    std::cout << "x after byValue(x): " << x << std::endl;

    byValue(cx);  // T 被推导为 int
    std::cout << "cx after byValue(cx): " << cx << std::endl;

    byValue(rx);  // T 被推导为 int
    std::cout << "rx after byValue(rx): " << rx << std::endl;

    return 0;
}
```

## 引用传递

```c++
#include <iostream>
#include <typeinfo>

template <typename T>
void byReference(T& param) {
    std::cout << "Type: " << typeid(T).name() << std::endl;
    param = 100; // 修改引用的值
}

int main() {
    int x = 42;
    const int cx = x;
    int& rx = x;

    byReference(x);   // T 被推导为 int，x 会被修改
    std::cout << "x: " << x << std::endl;

    // byReference(cx);  // T 被推导为 const int，尝试修改 const 引用会导致编译错误

    byReference(rx);  // T 被推导为 int，rx（即 x）会被修改
    std::cout << "rx: " << rx << std::endl;
    std::cout << "x: " << x << std::endl;

    return 0;
}
```

- `byReference(x)`中的 `param` 引用 `x`。
- `byReference(cx)`中的 `param` 引用 `cx`。
- `byReference(rx)`中的 `param` 引用 `x`，因为 `rx`是 `x`的引用。

## 按右值引用传递

​	`T&&`也可称为万能引用，可以**保留实参的左右值类型**。实参是左值推到为左值引用，实参是右值被推导为右值引用(move(a) -- int &&)或普通右值(42 -- int)。

```c++
#include <iostream>
#include <typeinfo>
#include <type_traits>

template <typename T>
void byRValueReference(T&& param) {
    std::cout << "T type: " << typeid(T).name() << std::endl;

    if (std::is_lvalue_reference<T>::value) {
        std::cout << "T is lvalue reference" << std::endl;
    } else if (std::is_rvalue_reference<T>::value) {
        std::cout << "T is rvalue reference" << std::endl;
    } else {
        std::cout << "T is neither lvalue nor rvalue reference" << std::endl;
    }

    if (std::is_lvalue_reference<decltype(param)>::value) {
        std::cout << "param is lvalue reference" << std::endl;
    } else if (std::is_rvalue_reference<decltype(param)>::value) {
        std::cout << "param is rvalue reference" << std::endl;
    } else {
        std::cout << "param is neither lvalue nor rvalue reference" << std::endl;
    }

    param = 100; // 修改引用的值
}

int main() {
    int x = 42;
    const int cx = x;

    byRValueReference(42);          // T 被推导为 int
    std::cout << "x after byRValueReference(42): " << x << std::endl;

    byRValueReference(x);           // T 被推导为 int&
    std::cout << "x after byRValueReference(x): " << x << std::endl;

    byRValueReference(std::move(x)); // T 被推导为 int&&
    std::cout << "x after byRValueReference(std::move(x)): " << x << std::endl;

    // byRValueReference(cx);         // T 被推导为 const int&

    return 0;
}
```

### 1.`byRValueReference(42)`

- 实参 `42` 是一个右值。
- 形参 `param`的类型是 `T&&`，此时 `T` 被推导为 `int`。
- `param` 是 `int&&` 类型的右值引用，引用的是右值 `42`。

### 2. `byRValueReference(x)`

- 实参 `x` 是一个左值。
- 形参 `param` 的类型是 `T&&` ，此时 `T` 被推导为 `int&`。
- `param` 是 `int& &&` ，根据引用折叠规则，变为 `int&` ，即左值引用，引用的是左值 `x`。

### 3. `byRValueReference(cx)`

- 实参 `cx` 是一个 `const int` 类型的左值。
- 形参 `param` 的类型是 `T&&` ，此时 `T` 被推导为 `const int&`。
- `param` 是 `const int& &&`，根据引用折叠规则，变为 `const int&`，即左值引用，引用的是左值 `cx`。

### 4. `byRValueReference(std::move(x))`

- 实参 `std::move(x)` 是一个右值引用。
- 形参 `param` 的类型是 `T&&`，此时 `T` 被推导为 `int&&` 。
- `param` 的类型是 `int&& &&` ，引用右值 `std::move(x)`。

## 完美转发

​	使用 `T&&` 可以实现完美转发，利用 `std::forward` 保留传递参数的值类别(左值或右值)。

```c++
#include <iostream>
#include <utility>

// 假设 byRValueReference 是一个接受参数的函数
void byRValueReference(int&& arg) {
    std::cout << "byRValueReference called with rvalue reference: " << arg << std::endl;
}

void byRValueReference(const int& arg) {
    std::cout << "byRValueReference called with const lvalue reference: " << arg << std::endl;
}

template <typename T>
void forwardExample(T&& param) {
    // std::forward 保留了传递参数的值类别
    byRValueReference(std::forward<T>(param));
}

int main() {
    int x = 42;
    forwardExample(x);          // T 被推导为 int&，byRValueReference 被调用时传递 int&
    forwardExample(42);         // T 被推导为 int，byRValueReference 被调用时传递 int&&
    forwardExample(std::move(x)); // T 被推导为 int&&，byRValueReference 被调用时传递 int&&
}
```

