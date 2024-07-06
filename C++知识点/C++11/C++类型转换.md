## C++类型转换

​	C++中类型转换分为两大类：**隐式类型转换**（Implicit Conversion）和**显式类型转换**（Explicit Conversion）。

隐式类型转换

隐式类型转换由编译器自动执行，无需程序员明确指示。它发生在以下几种情况：

**数值提升（Numeric Promotion）**小范围类型自动转换为大范围类型，例如char转换为int，short转换为int或long等。

```c++
#include <iostream>

int main() {
    char smallNumber = 'A'; // 'A' 的 ASCII 码值为 65
    int largeNumber = smallNumber; // 将 char 类型提升为 int 类型

    std::cout << "Small number (char): " << smallNumber << std::endl;
    std::cout << "Large number (int): " << largeNumber << std::endl;

    return 0;
}
```

**标准转换序列：**如从派生类指针到基类指针的转换。

```c++
#include <iostream>

class Base {
public:
    virtual void display() {
        std::cout << "Base class" << std::endl;
    }
};

class Derived : public Base {
public:
    void display() override {
        std::cout << "Derived class" << std::endl;
    }
};

int main() {
    Derived derivedObj;
    Base *basePtr = &derivedObj; // 将派生类指针转换为基类指针

    basePtr->display(); // 调用基类中的虚函数

    return 0;
}
```

**数组转换为指针：**数组名会隐式转换为其指向第一个元素的指针。

```c++
#include <iostream>

int main() {
    int arr[5] = {1, 2, 3, 4, 5};

    // 数组名隐式转换为指向第一个元素的指针
    int *ptr = arr;

    // 使用指针遍历数组并输出元素值
    for (int i = 0; i < 5; ++i) {
        std::cout << "Element " << i << ": " << *(ptr + i) << std::endl;
    }

    return 0;
}
```

**函数参数匹配**：当函数调用时，**实参类型会自动转换为形参类型**，只要转换是兼容的。

```c++
#include <iostream>

void display(int num) {
    std::cout << "Integer number: " << num << std::endl;
}

void display(double num) {
    std::cout << "Double number: " << num << std::endl;
}

int main() {
    int intValue = 10;
    double doubleValue = 5.5;

    // 调用 display(int)
    display(intValue);

    // 调用 display(double)
    display(doubleValue);

    return 0;
}
```

**构造函数初始化：**使用一个类型来初始化另一个类型时，如果目标类型有合适的构造函数，也会发生隐式转换。

```c++
#include <iostream>

class MyString {
private:
    char* buffer;

public:
    // 构造函数
    MyString(const char* initialInput) {
        if (initialInput != nullptr) {
            buffer = new char[strlen(initialInput) + 1];
            strcpy(buffer, initialInput);
        } else {
            buffer = nullptr;
        }
    }

    // 析构函数
    ~MyString() {
        delete[] buffer;
    }

    // 显示字符串内容
    void display() const {
        std::cout << buffer << std::endl;
    }
};

int main() {
    // 使用 const char* 初始化 MyString 对象
    MyString myString("Hello, World!");

    // 调用 display 方法显示字符串内容
    myString.display();

    return 0;
}
```

### **显式类型转换**

显式类型转换需要程序员明确指定，主要通过以下几种方式完成：

**C风格类型转换（C-Style Casts）：**使用 (type) expression 形式的语法。这种方式不推荐，因为它过于宽松，容易引起类型安全问题。

```c++
#include <iostream>

int main() {
    double d = 3.14;
    int i = (int)d; // 使用 C 风格类型转换将 double 转换为 int

    std::cout << "Double value: " << d << std::endl;
    std::cout << "Integer value: " << i << std::endl;

    return 0;
}
```

**静态转换（static_cast<type> expression）：**用于非多态类型间的转换，例如基本类型之间的转换、类层次结构中的向上转换（基类指针/引用转换为派生类指针/引用，但不安全）、枚举和整数类型之间的转换等。

```c++
#include <iostream>

class Base {
public:
    virtual void display() {
        std::cout << "Base class" << std::endl;
    }
};

class Derived : public Base {
public:
    void display() override {
        std::cout << "Derived class" << std::endl;
    }
};

int main() {
    // 向上转换：基类指针转换为派生类指针（不安全）
    Base* basePtr = new Derived();
    Derived* derivedPtr = static_cast<Derived*>(basePtr);
    derivedPtr->display();

    // 枚举和整数类型之间的转换
    enum class Color { Red, Green, Blue };
    int colorValue = static_cast<int>(Color::Green);
    std::cout << "Color value: " << colorValue << std::endl;

    return 0;
}
```

**动态转换（dynamic_cast<type> expression）**：主要用于多态类型（含有虚函数的类）的向下转换（派生类指针/引用转换为基类指针/引用），并且在运行时检查转换的安全性。如果转换失败，dynamic_cast会返回nullptr（指针）或抛出std::bad_cast异常（引用）。

```c++
#include <iostream>
#include <typeinfo>

class Animal {
public:
    virtual void sound() const { std::cout << "Some animal sound" << std::endl; }
    virtual ~Animal() {}
};

class Dog : public Animal {
public:
    void sound() const override { std::cout << "Woof!" << std::endl; }
};

class Cat : public Animal {
public:
    void sound() const override { std::cout << "Meow!" << std::endl; }
};

int main() {
    Animal* animalPtr = new Dog(); // 创建一个Dog对象，但通过基类指针引用

    // 尝试将基类指针转换为Dog的指针
    Dog* dogPtr = dynamic_cast<Dog*>(animalPtr);

    if (dogPtr != nullptr) {
        std::cout << "It's a Dog!" << std::endl;
        dogPtr->sound();
    } else {
        std::cout << "It's not a Dog." << std::endl;
    }

    delete animalPtr; // 记得释放内存

    return 0;
}
```

**重解释转换（reinterpret_cast<type> expression）**：用于低级的、不检查类型的转换，如**指针类型之间的转换**、**指针到整数的转换**等。这种转换可能导致未定义行为，应谨慎使用。

```c++
#include <iostream>

int main() {
    int value = 10;
    int* ptr = &value;

    // 将整数指针转换为另一种类型的指针（例如字符指针）
    char* charPtr = reinterpret_cast<char*>(ptr);

    // 修改通过 reinterpret_cast 转换后的指针指向的数据
    *charPtr = 'A';

    // 输出整数值和通过字符指针修改后的结果
    std::cout << "Original integer value: " << value << std::endl;
    std::cout << "Modified value through reinterpret_cast: " << *ptr << std::endl;

    return 0;
}
```

**const_cast<type> expression：**用来修改类型的const或volatile属性，但不改变类型的其他方面。主要用于去除常量属性以便修改变量，但应当确保这样做不会破坏程序的逻辑。

```c++
#include <iostream>

int main() {
    const int value = 10;

    // 尝试修改 const 变量的值，将 const 属性去除
    int& ref = const_cast<int&>(value);
    ref = 20;

    // 输出修改后的值
    std::cout << "Modified value: " << value << std::endl;

    return 0;
}
```

