## 可调用对象(Callable Objects)

在C++中，可调用对象（Callable Objects）是指可以像函数一样被调用的对象。它们可以采取多种形式，包括：

### 函数：

最基本的可调用对象就是普通的函数。

```c++
   void function() {
       // Do something
   }
```

### 函数指针：

指向函数的指针也是可调用对象。

```c++
   void (*functionPtr)() = &function;
   functionPtr(); // 调用函数
```

```c++
int print(int a, double b)
{
    cout << a << b << endl;
    return 0;
}
// 定义函数指针
int (*func)(int, double) = &print;

```

### Lambda表达式：

是C++11引入的一种便捷方式，用于定义匿名函数。

```c++
   auto lambda = []() {
       // Do something
   };
   lambda(); // 调用 Lambda 表达式
```

### 函数对象(Function Objects，或称为仿函数 Functors)：

是重载了`operator()`的类的实例。

```c++
   struct Functor {
       void operator()() {
           // Do something
       }
   };
   
   Functor functor;
   functor(); // 调用重载的 operator()
```

```c++
#include <iostream>
#include <string>
#include <vector>
using namespace std;

struct Test
{
    // ()操作符重载
    void operator()(string msg)
    {
        cout << "msg: " << msg << endl;
    }
};

int main(void)
{
    Test t;
    t("我是要成为海贼王的男人!!!");	// 仿函数
    return 0;
}
```

### 成员函数指针：

指向类的成员函数的指针。

```c++
   struct MyClass {
       void memberFunction() {
           // Do something
       }
   };
   
   void (MyClass::*memberFunctionPtr)() = &MyClass::memberFunction;
   MyClass obj;
   (obj.*memberFunctionPtr)(); // 调用成员函数
```

```c++
#include <iostream>
#include <string>
#include <vector>
using namespace std;

struct Test
{
    void print(int a, string b)
    {
        cout << "name: " << b << ", age: " << a << endl;
    }
    int m_num;
};

int main(void)
{
    // 定义类成员函数指针指向类成员函数
    void (Test::*func_ptr)(int, string) = &Test::print;
    // 类成员指针指向类成员变量
    int Test::*obj_ptr = &Test::m_num;

    Test t;
    // 通过类成员函数指针调用类成员函数
    (t.*func_ptr)(19, "Monkey D. Luffy");
    // 通过类成员指针初始化类成员变量
    t.*obj_ptr = 1;
    cout << "number is: " << t.m_num << endl;

    return 0;
}
```

​	在上面的例子中满足条件的这些可调用对象对应的类型被统称为**可调用类型**。C++中的可调用类型虽然具有比较统一的操作形式，但定义方式五花八门，这样在我们试图使用统一的方式保存，或者传递一个可调用对象时会十分繁琐。**C++11通过提供std::function 和 std::bind统一了可调用对象的各种操作。**

### 包装器：

是一种通用的可调用包装器，它可以存储、复制和调用任何可调用对象。它是一个类模板，可以容纳**除了类成员（函数）指针之外**的所有可调用对象。**通过指定它的模板参数，它可以用统一的方式处理函数、函数对象、函数指针，并允许保存和延迟执行它们。**

```c++
   #include <functional>
   //std::function<返回值类型(参数类型列表)> diy_name = 可调用对象;
   std::function<void()> func = function;
   func(); // 调用存储在 std::function 中的可调用对象
```

```c++
#include <iostream>
#include <functional>
using namespace std;

int add(int a, int b)
{
    cout << a << " + " << b << " = " << a + b << endl;
    return a + b;
}

class T1
{
public:
    static int sub(int a, int b)
    {
        cout << a << " - " << b << " = " << a - b << endl;
        return a - b;
    }
};

class T2
{
public:
    int operator()(int a, int b)
    {
        cout << a << " * " << b << " = " << a * b << endl;
        return a * b;
    }
};

int main(void)
{
    // 绑定一个普通函数
    function<int(int, int)> f1 = add;
    // 绑定以静态类成员函数
    function<int(int, int)> f2 = T1::sub;
    // 绑定一个仿函数
    T2 t;
    function<int(int, int)> f3 = t;

    // 函数调用
    f1(9, 3);
    f2(9, 3);
    f3(9, 3);

    return 0;
}
```

​	`std::function`可以将可调用对象进行包装，得到一个统一的格式，包装完成得到的对象相当于一个函数指针，和函数指针的使用方式相同，通过包装器对象就可以完成对包装的函数的调用了。

```c++
#include <iostream>
#include <functional>
using namespace std;

class A
{
public:
    // 构造函数参数是一个包装器对象
    A(const function<void()>& f) : callback(f)
    {
    }

    void notify()
    {
        callback(); // 调用通过构造函数得到的函数指针
    }
private:
    function<void()> callback;
};

class B
{
public:
    void operator()()
    {
        cout << "我是要成为海贼王的男人!!!" << endl;
    }
};
int main(void)
{
    B b;
    A a(b); // 仿函数通过包装器对象进行包装
    a.notify();

    return 0;
}
```

​	使用对象包装器`std::function`可以非常方便的将仿函数转换为一个函数指针，通过进行函数指针的传递，在其他函数的合适的位置就可以调用这个包装好的仿函数了。

​	另外，使用`std::function`作为函数的传入参数，可以将定义方式不相同的可调用对象进行统一的传递，这样大大增加了程序的灵活性。

### 绑定器：

生成新的可调用对象，将参数绑定到一个函数或函数对象的调用上。`std::bind`用来将可调用对象与其参数一起进行绑定。绑定后的结果可以使用`std::function`进行保存，并延迟调用到任何我们需要的时候。通俗来讲，它主要有两大作用：

- 将可调用对象与其参数一起绑定成一个仿函数。
- 将多元（参数个数为n，n>1）可调用对象转换为一元或者（n-1）元可调用对象，即只绑定部分参数。

```c++
#include <functional>
using namespace std::placeholders;
   
// 绑定非类成员函数/变量
//auto f = std::bind(可调用对象地址, 绑定的参数/占位符);
// 绑定类成员函/变量
//auto f = std::bind(类函数/成员地址, 类实例对象地址, 绑定的参数/占位符);

auto boundFunc = std::bind(&MyClass::memberFunction, _1);
MyClass obj;
boundFunc(obj); // 调用成员函数，并将 obj 作为参数
```

​	`std::bind`绑定器返回的是一个仿函数类型，得到的返回值可以直接赋值给一个`std::function`，在使用的时候我们并不需要关心绑定器的返回值类型，使用`auto`进行自动类型推导就可以了。

```c++
#include <iostream>
#include <functional>
using namespace std;

void callFunc(int x, const function<void(int)>& f)
{
    if (x % 2 == 0)
    {
        f(x);
    }
}

void output(int x)
{
    cout << x << " ";
}

void output_add(int x)
{
    cout << x + 10 << " ";
}

int main(void)
{
    // 使用绑定器绑定可调用对象和参数
    auto f1 = bind(output, placeholders::_1);
    for (int i = 0; i < 10; ++i)
    {
        callFunc(i, f1);
    }
    cout << endl;

    auto f2 = bind(output_add, placeholders::_1);
    for (int i = 0; i < 10; ++i)
    {
        callFunc(i, f2);
    }
    cout << endl;

    return 0;
}

```

```c++
#include <iostream>
#include <functional>
using namespace std;

void output(int x, int y)
{
    cout << x << " " << y << endl;
}

int main(void)
{
    // 使用绑定器绑定可调用对象和参数, 并调用得到的仿函数
    bind(output, 1, 2)();
    bind(output, placeholders::_1, 2)(10);
    bind(output, 2, placeholders::_1)(10);

    // error, 调用时没有第二个参数
    // bind(output, 2, placeholders::_2)(10);
    // 调用时第一个参数10被吞掉了，没有被使用
    bind(output, 2, placeholders::_2)(10, 20);

    bind(output, placeholders::_1, placeholders::_2)(10, 20);
    bind(output, placeholders::_2, placeholders::_1)(10, 20);

    return 0;
}
```

​	可调用对象包装器`std::function`是不能实现对类成员函数指针或者类成员指针的包装的，但是通过绑定器`std::bind`的配合之后，就可以完美的解决这个问题了。

```c++
#include <iostream>
#include <functional>
using namespace std;

class Test
{
public:
    void output(int x, int y)
    {
        cout << "x: " << x << ", y: " << y << endl;
    }
    int m_number = 100;
};

int main(void)
{
    Test t;
    // 绑定类成员函数
    function<void(int, int)> f1 = 
        bind(&Test::output, &t, placeholders::_1, placeholders::_2);
    // 绑定类成员变量(公共)
    function<int&(void)> f2 = bind(&Test::m_number, &t);

    // 调用
    f1(520, 1314);
    f2() = 2333;
    cout << "t.m_number: " << t.m_number << endl;

    return 0;
}
```

​	`f1`的类型是`function<void(int, int)>`，通过使用`std::bind`将`Test`的成员函数`output`的地址和对象t绑定，并转化为一个仿函数并存储到对象`f1`中。

​	使用绑定器绑定的类成员变量`m_number`得到的仿函数被存储到了类型为`function<int&(void)>`的包装器对象`f2`中，并且可以在需要的时候修改这个成员。其中`int`是绑定的类成员的类型，并且允许修改绑定的变量，因此需要指定为变量的引用，由于没有参数因此参数列表指定为`void`。

### 应用场景：

1. **标准库算法**：C++标准库中的算法（如 `std::sort`, `std::transform`, `std::for_each` 等）通常接受函数指针、函数对象或者Lambda表达式作为参数，以定义操作行为。

```c++
std::vector<int> vec = {3, 1, 4, 1, 5};
std::sort(vec.begin(), vec.end(), [](int a, int b) { return a < b; });
```

2. **线程构造**：在创建 `std::thread` 对象时，可调用对象用于指定线程执行的函数。

```c++
std::thread t([]() {
   // Do something in a new thread
});
t.join();
```

3. **事件处理和回调**：在设计事件驱动程序或需要回调的系统时，可调用对象用于传递和执行回调函数。

```c++
void onEvent(std::function<void()> callback) {
   // Event triggered
   callback();
}
onEvent(lambda);
```

4. **构建函数包装器**：`std::function` 可用于创建函数签名的包装器，允许存储和调用任何匹配签名的可调用对象。

```c++
std::function<void(int)> f = [](int x) { /* ... */ };
```

5. **延迟计算**：可调用对象可用于实现延迟计算，即将计算推迟到值实际需要的时候再执行。

```c++
auto lazyValue = std::bind([](int x) { return x * x; }, 5);
// Some code...
int value = lazyValue(); // Calculate now
```

6. **策略模式**：函数对象可以作为策略模式中的策略实现，允许在运行时选择不同的算法。

```c++
class Strategy {
public:
   virtual void execute() = 0;
};

class ConcreteStrategyA : public Strategy {
public:
   void execute() override {
       // Implementation A
   }
};

void context(Strategy& strategy) {
   strategy.execute();
}

ConcreteStrategyA strategyA;
context(strategyA);
```

7. **绑定参数**：`std::bind` 可用于预绑定参数到特定值，方便后续调用。

```c++
void foo(int a, int b) {
   // do something
}
auto bindFoo = std::bind(foo, 42, std::placeholders::_1);
bindFoo(7); // equivalent to foo(42, 7)
```

8. **自定义比较函数**：在排序或其他需要比较的操作中使用自定义的比较逻辑。

```c++
std::set<int, std::function<bool(int, int)>> customOrder([](int a, int b) {
   return a > b; // custom order logic
});
```