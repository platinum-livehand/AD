# `typedef` 和 `using` 的使用示例

## 1. 为基本数据类型创建别名

```c++
// 使用 typedef
typedef unsigned long ulong;

// 使用 using
using ulong = unsigned long;

ulong myNumber = 123456789;
```

## 2. 为指针创建别名

```c++
// 使用 typedef
typedef char* pChar;

// 使用 using 
using pChar = char*;

pChar myString = "Hello, World!";
```

## 3. 为数组类型创建别名

```c++
// 使用 typedef 
typedef int intArray[10];

// 使用 using 
using intArray = int[10];

intArray numbers = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
```

## 4. 为结构体创建别名

```c++
// 使用 typedef
typedef struct 
{
    int x;
    int y;
} Point;

// 使用 using 
using Point = struct 
{
    int x;
    int y;
};

Point p1 = {10, 20};
```

## 5. 为函数指针创建别名

```c++
// 使用 typedef
typedef void (*FunctionPointer)();

// 使用 using 
using FunctionPointer = void (*)();

// 函数定义
void myFunction() 
{
    // Do something
}

FunctionPointer fp = myFunction;
```

## 6. 总结

​	`using` 和 `typedef` 主要区别在于， `using` 提供了更好的语法一致性，尤其是对于模板命名。例如，你可以定义一个模板别名如下：

```c++
template <class T>
using Ptr = T*;

Ptr<int> intPtr; // 指向int型的指针
Ptr<double> doublePtr; // 指向double型的指针
```

`typedef` 无法做到这一点。然而，对于非模板类型的别名定义， `typedef` 和 `using` 可以互换使用。