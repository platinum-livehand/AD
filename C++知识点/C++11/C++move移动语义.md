## 移动语义

​	C++11 引入的移动语义是对 C++ 语言的一大改进，它提供了一种更有效的资源管理方式，特别是在处理大量数据或数据密集型对象时。移动语义通过引入右值引用(Right-Value Reference，标记为 `&&`)和两个新的构造函数——移动构造函数(Move Constructor)和移动赋值运算符(Move Assignment Operator)——来实现。这些新特性允许开发者优化程序性能，减少不必要的对象拷贝，从而提高效率。

## 右值引用

​	右值引用时引入移动语义的基础。它允许我们引用一个**临时对象**(右值)，而非持久存在的对象(左值)。通过这种方式，我们可以重用临时对象的资源，避免不必要的拷贝。右值引用通过添加`&&`到类型声明来定义。

## 移动构造函数和移动赋值运算符

​	**移动构造函数**和**移动赋值运算符**允许一个对象**移动**(Move)另一个对象的资源，而不是**赋值**(Copy)它们。

- **移动构造函数：**允许从一个即将销毁的对象中移动资源，而非复制。它的签名类似于`ClassName(ClassName&& other)`。
- **移动赋值运算符：**允许将一个即将销毁的对象的资源赋值给另一个对象。它的签名类似于`ClassName& operator=(ClassName&& other)`。

```c++
#include <iostream>
#include <vector>

class HugeData {
public:
    std::vector<int> data; // 假设这里有大量数据
    
    // 构造函数
    HugeData(int size) : data(size) {}
    
    // 移动构造函数
    HugeData(HugeData&& other) noexcept : data(std::move(other.data)) {
        std::cout << "移动构造函数被调用" << std::endl;
    }
    
    // 移动赋值运算符
    HugeData& operator=(HugeData&& other) noexcept {
        // 右值引用本身也是左值，所以可以取地址 &other
        if (this != &other) {
            data = std::move(other.data);
            std::cout << "移动赋值运算符被调用" << std::endl;
        }
        return *this;
    }
    
    // 禁用拷贝构造函数和拷贝赋值运算符
    HugeData(const HugeData&) = delete;
    HugeData& operator=(const HugeData&) = delete;
};

int main() {
    HugeData original(10000); // 创建一个含有大量数据的对象
    
    // move() 将左值 original 转化为右值
    // 通过移动构造函数创建一个新对象，"移动" original 的资源
    HugeData movedTo = std::move(original); 
    
    // 原始对象现在不再拥有数据
    std::cout << "原始数据大小: " << original.data.size() << std::endl;
    std::cout << "移动后数据大小: " << movedTo.data.size() << std::endl;

    return 0;
}
```

​	在这个例子中，`HugeData`类包含一个可能包含大量数据的`vector`。通过实现移动构造函数和移动赋值运算符，当一个`HugeData`对象被移动时，它实际上时将数据的所有权从一个对象转移到另一个对象，而不进行昂贵的深拷贝。

## 为什么需要移动语义

​	在 C++11 之前，对象间的数据传递通常涉及到深拷贝，这可能导致不必要的性能开销。对于包含动态分配内存或其他资源(如文件句柄、网络连接等)的对象来说，深拷贝尤其耗时。移动语义通过允许资源所有权转移来解决这个问题，从而提高了性能并减少了资源消耗。

## `std::move`的基本用法

```c++
#include <vector>
#include <iostream>
#include <utility> // 包含std::move

void process(std::vector<int>&& vec) {
    // 处理vec...
    std::cout << "处理向量，大小为: " << vec.size() << std::endl;
}

int main() {
    std::vector<int> myVec(1000, 1); // 一个包含1000个元素的vector
    process(std::move(myVec)); // 将myVec转换为右值引用
    std::cout << "myVec的大小现在是: " << myVec.size() << std::endl;
    // 注意：此时myVec的状态未定义，不应再使用
    return 0;
}
```

​	在这个例子中，我们使用`std::move`将`myVec`转换为**右值引用**，允许我们在调用`process`函数时使用移动语义。这意味着`myVec`的内容被**移动**到函数参数`vec`中，而不是被复制。这可以显著减少内存使用和提高性能。但是，一旦移动发生，源对象(本例中为`myVec`)处于一个有效、但未定义的状态，因此在移动操作后继续使用它时**不安全**的，除非你重新复制或者重置它。**`std::move`仅仅是执行一个类型转换，并不保证移动操作一定会发生。是否真正发生移动操作取决于对象的类型和它提供移动构造函数或赋值运算符的实现。**

```c++
#include <iostream>
#include <utility> // std::move
#include <algorithm> // std::copy

class DynamicArray {
public:
    int* data;
    size_t size;

    // 构造函数
    DynamicArray(size_t size) : data(new int[size]), size(size) {}

    // 析构函数
    ~DynamicArray() { delete[] data; }

    // 移动构造函数
    DynamicArray(DynamicArray&& other) noexcept : data(nullptr), size(0) {
        data = other.data;
        size = other.size;
        other.data = nullptr;
        other.size = 0;
    }

    // 移动赋值运算符
    DynamicArray& operator=(DynamicArray&& other) noexcept {
        if (this != &other) {
            delete[] data;
            data = other.data;
            size = other.size;
            other.data = nullptr;
            other.size = 0;
        }
        return *this;
    }

    // 禁用拷贝构造函数和拷贝赋值运算符
    DynamicArray(const DynamicArray&) = delete;
    DynamicArray& operator=(const DynamicArray&) = delete;
};

DynamicArray createArray(size_t size) {
    return DynamicArray(size);
}

int main() {
    DynamicArray arr = createArray(10); // 调用移动构造函数
    DynamicArray another = std::move(arr); // 显式调用移动赋值运算符
    return 0;
}
```

​	上面代码中，我们把源对象中的资源移动后，明确地将其指针置为`nullptr`，这样之后操作源对象，对新对象就不会造成影响。