# const和constexpr

## 1.const

在C++11之前只有const关键字，从功能上来说这个关键字有双重语义：**变量只读**，**修饰常量**，举一个简单的例子：

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

## 2.constexpr

在C++11中添加了一个新的关键字`constexpr`，这个关键字是用来修饰常量表达式的。所谓常量表达式，指的就是由多个（≥1）常量（值不会改变）组成并且在编译过程中就得到计算结果的表达式。

C++ 程序从编写完毕到执行分为四个阶段：**预处理**、 **编译**、**汇编**和**链接**4个阶段，得到可执行程序之后就可以运行了。需要额外强调的是，常量表达式和非常量表达式的计算时机不同，**非常量表达式只能在程序运行阶段计算出结果**，但是**常量表达式的计算往往发生在程序的编译阶段**，这可以极大提高程序的执行效率，因为表达式只需要在编译阶段计算一次，节省了每次程序运行时都需要计算一次的时间。

在C++11中添加了`constexpr`关键字之后就可以在程序中使用它来`修饰常量表达式`，用来提高程序的执行效率。在使用中建议将 `const` 和 `constexpr` 的功能区分开，即凡是表达“**只读**”语义的场景都使用 `const`，表达“**常量**”语义的场景都使用 `constexpr`。


在定义常量时，`const` 和 `constexpr` 是等价的，都可以在程序的编译阶段计算出结果，例如：

```c++
const int m = f();  // 不是常量表达式，m的值只有在运行时才会获取。
const int i = 520;    // 是一个常量表达式
const int j = i + 1;    // 是一个常量表达式

constexpr int i = 520;    // 是一个常量表达式
constexpr int j = i + 1;    // 是一个常量表达式
```

对于 C++ 内置类型的数据，可以直接用 `constexpr` 修饰，但如果是自定义的数据类型（用 `struct` 或者 `class` 实现），直接用 `constexpr` 修饰是不行的。

```c++
// 此处的constexpr修饰是无效的
constexpr struct Test
{
    int id;
    int num;
};
```

如果要定义一个结构体/类常量对象，可以这样写：

```c++
struct Test
{
    int id;
    int num;
};

int main()
{
    constexpr Test t{ 1, 2 };
    constexpr int id = t.id;
    constexpr int num = t.num;
    t.num += 100;	// error，不能修改常量
    cout << "id: " << id << ", num: " << num << endl;

    return 0;
}
```

