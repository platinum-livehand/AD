## const

在 C++11 之前只有`const`关键字，从功能上来说这个关键字有双重语义：**变量只读**，**修饰常量**，举一个简单的例子：

```c++
void func(const int num)
{
    const int count = 24;
    int array[num];            // error，num是一个只读变量，不是常量
    int array1[count];         // ok，count是一个常量

    int a1 = 520;
    int a2 = 250;
    const int& b = a1;
    b = a2;                         // error
    a1 = 1314;
    cout << "b: " << b << endl;     // 输出结果为1314
}
```

- 函数`void func(const int num)`的参数`num`表示这个变量是只读的，但不是常量，因此使用`int array[num];` 这种方式定义一个数组，编译器是会报错的，提示`num不可用作为常量来使用`。

- `const int count = 24;`中的`count`却是一个常量，因此可以使用这个常量来定义一个静态数组。

**变量只读并不等价于常量**，二者是两个概念不能混为一谈，分析一下这句测试代码`const int& b = a1;`：

- `b`是一个常量的引用，所以`b`引用的变量是不能被修改的，也就是说`b = a2`; 这句代码语法是错误的。
- 在`const`对于变量`a1`是没有任何约束的，`a1`的值变了`b`的值也就变了。
- 引用`b`是只读的，但是并不能保证它的值是不可改变的，也就是说它不是常量。

## constexpr

​	`constexpr`是 C++11 引入的一个关键字，用于表示可以在编译器求值的常量表达式。与 `const`不同的是，`constexpr`强调的是编译器计算，而 `const`强调的是运行期不可修改性。

### `constexpr` 常量

```c++
constexpr int max_value = 100;
```

​	与 `const` 类似，这里 `max_value` 被定义为一个常量，但 `constexpr` 确保了 `max_value` 可以在编译期被计算和使用。

### `constexpr` 函数

```c++
constexpr int square(int x) {
    return x * x;
}
```

​	在这里，`square` 函数被定义为 `constexpr`，意味着如果在编译期传入常量参数，该函数的结果也将在编译器计算。 

### `constexpr` 对象

```c++
class MyClass {
public:
    constexpr MyClass(int x) : value(x) {}
    constexpr int getValue() const { return value; }
private:
    int value;
};

constexpr MyClass obj(10);
constexpr int val = obj.getValue();
```

​	在这个例子中，`MyClass` 类的构造函数和成员函数被定义为 `constexpr`，因此可以在编译期创建 `obj` 对象并获取其值。

## const 和 constexpr 的区别

### 编译期和运行期

- `const` 强调的是运行期不可修改性，即变量在初始化后，其值在程序运行期间不能改变。
- `constexpr`强调的是编译期求值，即表达式的值

### 函数和类的使用

- `const` 函数主要用于声明在函数内部不能修改对象的成员变量，保证函数的只读性。
- `constexpr`函数则用于声明可以在编译期进行求值的函数，提高代码的执行效率。

