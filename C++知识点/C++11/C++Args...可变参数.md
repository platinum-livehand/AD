## 可变参数

​	C++中的**可变参数**(Variadic Arguments)是指在函数定义时，允许传入不确定数量的参数。这在实现类似`printf`这样需要处理不同数量和类型的参数的函数时非常有用。C++提供了几种方式来处理可变参数，主要包括传统C+风格的方法和现代C++的模板元编程技术。

## C风格的可变参数处理

使用`<cstdarg>`库：这涉及到几个宏，如`va_list`，`va_start`，`va_arg`，和`va_end`。

基本步骤如下：

- 声明一个`va_list`类型的变量来存储参数列表。
- 使用`va_start`宏初始化这个变量，传入最后一个已知参数的地址。
- 使用`va_arg`宏依次获取参数，需要指定参数类型。
- 使用完毕后，用`va_end`宏清理变量。

```c++
#include <iostream>
#include <cstdarg>

double average(int count, ...) {
    va_list args;
    double sum = 0.0;
    va_start(args, count);
    for (int i = 0; i < count; ++i) {
        sum += va_arg(args, double);
    }
    va_end(args);
    return sum / count;
}

int main() {
    std::cout << average(3, 10.0, 20.0, 30.0) << std::endl;
    return 0;
}
```

## C++模板的可变参数模板(Varadic Templates)

​	可变参数模板：C++11引入了可变参数模板，这是一种更为类型安全和灵活的处理可变参数的方法。通过递归模板和模板参数包，可以在编译时处理任意数量和类型的参数。

### 1. 普通函数使用

```c++
#include <iostream>

template<typename T>
void print(T value) {
    std::cout << value << std::endl;
}

// typename... Args 定义了一个参数包模板
template<typename T, typename... Args>
// Args... 定义了一个函数参数包args
void print(T first, Args... args) {
    std::cout << first << ", ";
    // args... 参数包展开
    print(args...); // 递归展开参数包
}

int main() {
    print(1, 2.0, "Hello", 'A');
    return 0;
}
```

### 2. 类的成员函数使用

```c++
#include <iostream>

class ExampleClassTemplates {
public:
    template<typename T, typename... Args>
    void printInfo(T first, Args... args) {
        std::cout << first << " ";
        printMore(args...); // 递归调用处理剩余参数
    }

private:
    template<typename T>
    void printMore(T last) {
        std::cout << last << std::endl;
    }

    template<typename T, typename... Args>
    void printMore(T first, Args... args) {
        std::cout << first << ", ";
        printMore(args...);
    }
};

int main() {
    ExampleClassTemplates example;
    example.printInfo("Welcome", "to", "the", "world", "of", "C++", 20);
    return 0;
}
```

### 3. 工厂模式示例(使用 C++ 可变参数模板)

​	假设我们有一个抽象的`Shape`基类，以及几个派生类`Circle`，`Rectangle`，和`Triangle`。我们想通过一个工厂类`ShapeFactory`来创建这些不同形状的对象，每个形状的创建可能需要不同数量或类型的参数。

​	下面例子`ShapeFactory`类通过一个可变参数模板`create`，能够根据传入的类型和参数创建不同形状的对象。这种方法不仅提高了代码的灵活性，也保持了良好的封装性和可拓展性。

```C++
// C++ 14
#include <iostream>
#include <memory>

// 抽象基类 Shape
class Shape {
public:
    virtual ~Shape() {}
    virtual void draw() const = 0;
};

// 具体形状类
class Circle : public Shape {
public:
    Circle(double radius) : radius_(radius) {}
    void draw() const override { std::cout << "Drawing Circle with radius: " << radius_ << std::endl; }
private:
    double radius_;
};

class Rectangle : public Shape {
public:
    Rectangle(double width, double height) : width_(width), height_(height) {}
    void draw() const override { std::cout << "Drawing Rectangle with width: " << width_ << " height: " << height_ << std::endl; }
private:
    double width_, height_;
};

class Triangle : public Shape {
public:
    Triangle(double base, double height) : base_(base), height_(height) {}
    void draw() const override { std::cout << "Drawing Triangle with base: " << base_ << " height: " << height_ << std::endl; }
private:
    double base_, height_;
};

// 工厂类，使用可变参数模板
class ShapeFactory {
public:
    template<typename T, typename... Args>
    static std::unique_ptr<Shape> create(Args&&... args) {
        // 保留每个参数的类型(左值或右值)实现完美转发
        return std::make_unique<T>(std::forward<Args>(args)...);
    }
};

int main() {
    auto circle = ShapeFactory::create<Circle>(5.0);
    circle->draw();

    auto rectangle = ShapeFactory::create<Rectangle>(4.0, 6.0);
    rectangle->draw();

    auto triangle = ShapeFactory::create<Triangle>(3.0, 7.0);
    triangle->draw();

    return 0;
}
```