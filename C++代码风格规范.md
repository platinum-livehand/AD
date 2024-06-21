# 1. 头文件规范

## 1.1. 头文件应该可以独立编译

- 头文件要有头文件防护符，并导入它所需的所有其他头文件。
- 建议直接在头文件中提供 **内联函数** 和 **模板** 的定义。

**示例 - 模板定义通常放在头文件中**

```c++
#ifndef MATH_UTILS_H
#define MATH_UTILS_H

template <typename T>
T add(T a, T b) {
    return a + b;
}

#endif // MATH_UTILS_H
```

​	特定情况下，可以将定义放在该源文件中。这样做有助于提高编译器的效率，同时保持项目结构的清晰和管理的简单。

**特定情况 - 所有实例化都在单个源文件中**

```c++
#include <iostream>

template <typename T>
T add(T a, T b) {
    return a + b;
}

// 显式实例化
template int add<int>(int, int);
template double add<double>(double, double);

int main() {
    std::cout << add(1, 2) << std::endl; // 使用int实例化
    std::cout << add(1.1, 2.2) << std::endl; // 使用double实例化
    return 0;
}
```

## 1.2. `#define` 包含保护的命名规范

- 所有头文件都应该用 `#define` 防护符来防止重复导入。防护符的格式是: `<项目>_<路径>_<文件名>_H_`	

例如， `foo` 项目中的文件 `foo/src/bar/baz.h` 应该有如下防护:

```c++
#ifndef FOO_BAR_BAZ_H_
#define FOO_BAR_BAZ_H_
...
#endif  // FOO_BAR_BAZ_H_
```

## 1.3. 显式地导入使用的依赖

- 若代码文件或头文件引用了其他地方定义的符号，该文件应该直接导入 `#include` 提供该符号的声明或者定义的头文件。

例如，<u>若 `foo.cc` 使用了 `bar.h` 的符号，就需要导入 `bar.h`， 即使 `foo.h` 已经导入了 `bar.h`</u>。

## 1.4. 尽量避免使用前向声明，应该导入你所需的头文件

**定义：**

​	前向声明，是没有对应定义的声明。前向声明可以避免在 <u>头文件</u> 中直接使用 `#include`，这样做的主要目的是减少编译依赖和编译时间。然而，一旦你需要在实现文件（.cpp）中访问被前向声明的类的具体实现（比如创建对象、访问成员函数或成员变量），你就需要包含相应的头文件以提供类的完整定义。例如，

```c++
// ClassB.h
#ifndef CLASS_B_H
#define CLASS_B_H

class ClassB {
public:
    ClassB();
    ~ClassB();
    void someMethod();
};

#endif

// ClassB.cpp
#include "ClassB.h"

ClassB::ClassB() {
    // constructor implementation
}

ClassB::~ClassB() {
    // destructor implementation
}

void ClassB::someMethod() {
    // method implementation
}
```

```c++
// ClassA.h
#ifndef CLASS_A_H
#define CLASS_A_H

class ClassB; // 前向声明

class ClassA {
public:
    ClassA();
    ~ClassA();
    void anotherMethod(ClassB* cb);
};

#endif

// ClassA.cpp
#include "ClassA.h"
#include "ClassB.h" // 包含 ClassB 的头文件，以获取 ClassB 的完整定义

ClassA::ClassA() {
    // constructor implementation
}

ClassA::~ClassA() {
    // destructor implementation
}

void ClassA::anotherMethod(ClassB* cb) {
    cb->someMethod(); // 可以调用 ClassB 的方法，因为我们在这个文件中包含了 ClassB 的完整定义
}
```

​	<u>应该尽量避免为其他项目定义的实体提供前向声明</u>。

## 1.5. 只把10行以下的小函数定义为内联

- 内联那些有循环或者 `switch` 语句的函数通常得不偿失。
- 内联函数不能将声明与定义分离。

## 1.6. `#include` 的路径书写及顺序规范

​	头文件的路径<u>相对于项目源码目录</u>，<u>不能出现 UNIX 目录别名</u>` .` (当前目录) 或 `..` (上级目录)。

例如，应该按如下方式导入 `google-awesome-project/src/base/logging.h`:

```c++
#include "base/logging.h"
```

​	在 `dir/foo.cc` 或 `dir/foo_test.cc` 这两个实现或测试 `dir2/foo2.h` 内容的文件中，按如下顺序导入头文件 :

1.  `dir2/foo2.h`
2. 空行
3.  `C` 语言系统文件(使用方括号加 `.h` 扩展名的头文件)，例如 `<unistd.h>` 和 `<stdlib.h>` 。
4. 空行
5.  `C++` 标准库头文件(不含拓展名)，例如 `<algorithm>` 和 `<cstddef>` 。
6. 空行
7. <u>其他第三方库</u>的 `.h` 文件，例如 `<QVector>` 和 `<QString>`。
8. 空行
9. <u>本项目</u>的 `.h` 文件

​	且每个分组内部的导入语句应该按字母序排列。

例如，`google-awesome-project/src/foo/internal/fooserver.cc` 的导入语句如下:

```c++
#include "foo/server/fooserver.h"

#include <sys/types.h>
#include <unistd.h>

#include <string>
#include <vector>

#include "base/basictypes.h"
#include "foo/server/bar.h"
#include "third_party/absl/flags/flag.h"
```

# 2. 作用域

## 2.1. 命名空间

​	使用命名空间可以将全局作用域划分为独立的、有名字的作用域，因此可以有效防止全局作用域中的命名冲突。**命名空间只能定义在全局或者命名空间嵌套。一个工程中同名的命名空间最终会被合并为一个命名空间**。

- 要在命名空间 `namespace` 内放置代码。
- 遵守 命名空间命名 规则。
- 使用注释给命名空间收尾(注明命名空间的名字)。
- <u>在导入语句、gflags声明/定义以及其他命名空间的类的前向声明之后</u>，用命名空间包裹整个源代码文件:

```c++
// .h 文件
#include <iostream>
#include <vector>

// 其他命名空间的类的前向声明
namespace external {
    class ExternalClass;
}

// 错误的其他命名空间类的前向声明
// class external::ExternalClass;

// 你的命名空间开始
namespace my_namespace {

// 你的命名空间中的类定义
class MyClass {
public:
    void DoSomething();
};

} // namespace my_namespace
```

```c++
// .cc 文件
namespace my_namespace {
    //函数定义位于命名空间中
    void MyClass::DoSomething() {
        
    }
    
} // namespace my_namespace
```

- 不要在 `std` 命名空间内声明任何东西。在 `std` 命名空间内声明实体是未定义行为，会损害可移植性。
- 不要前向声明标准库的类。标准库的头文件已经包含了所需的所有声明，因此应当直接包含对应的头文件以确保正确的声明。

```C++
#include <vector> // 正确做法：包含头文件来使用 std::vector

// 错误做法：尝试前向声明 
// std::vector

// 错误做法：在 std 命名空间添加声明
// namespace std {
//     template <typename T>
//     class vector;
// }

int main() {
    std::vector<int> myVector;
    // ... 使用 myVector ...
    return 0;
}
```

- 禁止使用 `using` 指令引入命名空间的所有符号

```c++
// 禁止：这会污染命名空间
using namespace std;
```

- 除了在明显标注为内部使用的命名空间内，<u>不要让头文件引入命名空间的别名</u>。

```c++
// 在 .cc 中, 用别名缩略常用的名称.
namespace baz = ::foo::bar::baz;
```

```c++
// 在 .h 中, 用别名缩略常用的命名空间.
namespace librarian {
namespace impl {  // 仅限内部使用, 不是 API.
namespace sidetable = ::pipeline_diagnostics::sidetable;
}  // namespace impl

inline void my_inline_function() {
  // 一个函数 (f或方法) 中的局部别名.
  namespace baz = ::foo::bar::baz;
  ...
}
}  // namespace librarian
```

​	这种做法在实现文件（`.cpp`文件）中是非常方便的，因为它可以减少打字的工作量，并使代码更清晰。然而，如果这样的别名出现在头文件中，它就成为了头文件公开的一部分，意味着使用这个头文件的任何人都能看到并使用这个别名。

- 禁止内联命名空间。
- 鼓励新的代码使用单行的嵌套命名空间声明，但不强制要求。

```c++
namespace foo::bar {
...
}  // namespace foo::bar
```

**命名空间嵌套：**

```c++
namespace outer {
    int value = 10; // outer::value

    namespace inner {
        int value = 20; // outer::inner::value
    }
}

int main() {
    std::cout << outer::value << std::endl;        // 输出10
    std::cout << outer::inner::value << std::endl; // 输出20
    return 0;
}
```

**内联命名空间：**

​	内联命名空间通常用于版本控制，允许在不破坏二进制兼容性的情况下，向库中添加新的或修改现有的定义。

```c++
namespace outer {
    inline namespace inner {
        int value = 20;
    }
}

int main() {
    std::cout << outer::value << std::endl; // 输出20，不需要指定inner
    std::cout << outer::inner::value << std::endl; // 也可以这样访问，输出20
    return 0;
}
```

## 2.2. 内部链接

​	所有放入 **匿名命名空间** 中的 **声明** 默认为内部链接。声明为 `static` 的函数和变量也会内部链接。这意味着其他文件不能访问你声明的任何事物。即使另一个文件声明了一摸一样的名称，这两个实体也都是相互独立的。

- 建议 `.cc` 文件中所有不需要外部使用的代码采用内部链接。不要在 `.h` 文件中使用内部链接。

```c++
// MyClass.h

#ifndef MYCLASS_H
#define MYCLASS_H

class MyClass {
public:
    MyClass();
    void publicMethod();
    // ...
};

#endif // MYCLASS_H
```

```c++
// MyClass.cc

#include "MyClass.h"

#include <iostream>

namespace {
    // 该辅助函数只在本文件内可见
    void internalHelperFunction() {
        std::cout << "This function is internal to MyClass.cc" << std::endl;
    }
}	// namespace

MyClass::MyClass() {
    // Constructor implementation
}

void MyClass::publicMethod() {
    internalHelperFunction();  // 使用内部辅助函数
    // Public method implementation
}
```

## 2.3. 非成员函数、静态成员函数和全局函数

​	有时我们需要定义一个和 **类的实例无关的函数** 。这样的函数可以定义为 **静态成员函数** 或者 **非成员函数**。非成员函数不应该依赖外部变量，且大部分情况下应该位于命名空间中。不要仅仅为了给静态成员分组而创建一个新类；这相当于给所有名称添加了一个公共前缀，而这样的分组通常是不必要的。

- 不要使用全局函数，非成员函数放入命名空间。
- 不要为了给静态成员分组而使用类。
- 类的静态方法应当和类的实例或静态数据紧密相关。

```c++
class BankAccount {
public:
    static double interestRate; // 类的静态数据成员

    static void updateInterestRate(double newRate) { // 类的静态方法
        interestRate = newRate;
    }

    // 类的构造函数
    BankAccount(double balance) {
        this->balance = balance;
    }

    // 计算利息的实例方法
    double calculateInterest() const {
        return balance * interestRate;
    }

private:
    double balance; // 类的实例数据成员
};

// 初始化静态成员
double BankAccount::interestRate = 0.05;
```

​	在这个例子中，`interestRate` 是所有 `BankAccount` 对象共享的静态数据成员，而 `updateInterestRate` 是一个静态方法，用于修改这个共享的利率。这个静态方法与类的静态数据紧密相关，因为它直接操作这些数据。另一方面，`calculateInterest` 方法是一个实例方法，它依赖于每个 `BankAccount` 实例的具体 `balance`，以及共享的 `interestRate` 来计算利息。

## 2.4. 局部变量

## 2.5. 静态和全局变量

​	具有静态储存周期的变量

1、全局变量：在函数外部定义的变量，默认具有静态存储周期。这些变量在所有函数之外定义，可以被程序中的任意函数访问。

```c++
int globalVar; // 全局变量，具有静态存储周期
```

2、静态局部变量：在函数内部使用 `static` 关键字声明的局部变量。尽管它们在函数内部定义，它们的生命周期也是整个程序的执行期间，而不是像通常的局部变量那样在每次函数调用时创建和销毁。

```c++
void function() {
   static int staticVar = 0; // 静态局部变量，具有静态存储周期
   staticVar++;
}
```



## 2.6. thread_local 变量

# 3. 类

## 3.1. 构造函数的职责

## 3.2. 隐式类型转换

- 不要定义隐式类型转换。对于转换运算符和单参数构造函数，请使用 `explicit` 关键字。

## 3.3. 可拷贝类型和可移动类型

## 3.4.  结构体 VS. 类

- 仅当只有数据成员时使用 `struct` ，其它一概使用 `class` 。

## 3.5. 结构体 VS. pair 和 tuple

## 3.6. 继承

- 使用组合常常比使用继承更合理。如果使用继承的话，定义为 `public` 继承。
- 将 `protected` 的变量和函数限制为那些可能需要从子类访问的函数。
- 使用 `override` 关键字来标识重载的虚函数或者虚的析构函数。重载时不要使用 `virtual` 。

## 3.7. 运算符重载

## 3.8. 存取控制

## 3.9. 声明顺序

# 4. 函数

## 4.1.  输入和输出

- 倾向于按值返回，否则按引用返回。避免返回指针，除非它可以为空。
- **非可选输入参数**通常是值参或 `const` 引用，**非可选输出参数**或**输入/输出参数**通常应该是引用(不能为空)。
- 对于**可选的参数**，通常使用 `std::optional` 来表示可选的按值输入，使用 `const` 指针来表示可选的其他输入。使用非常量指针来表示可选输出和可选输入/输出参数。
- 避免在函数中使用 `const` 引用来绑定临时变量，因为这可能导致引用的对象在函数执行期间变得无效，即出现悬垂引用。

例如，

```c++
const std::string& getTemporaryString() {
    return std::string("Temporary String");
}

void useReference(const std::string& ref) {
    // 在这个函数内部，ref 是有效的。
    std::cout << ref << std::endl;
    // 但是，一旦这个函数结束，ref 所引用的临时字符串将销毁。
}

int main() {
    const std::string& tempRef = getTemporaryString();
    // 这里 tempRef 是有效的，因为它延长了临时对象的生命周期。

    useReference(tempRef);
    // useReference 函数运行完毕后，tempRef 指向的临时对象被销毁。
    // 因此，tempRef 现在是一个悬垂引用。

    // 下面使用 tempRef 会是未定义行为，因为它指向的临时对象已经不再存在。
    // std::cout << tempRef << std::endl; // 未定义行为！不要这么做。

    return 0;
}
```

**定义：**

1. 非可选输入参数：这些函数是必须要有的参数，没有它们函数不能完成既定的功能。

- 值参：当参数是基本数据类型（如`int`、`char`）或者对象不大且复制开销小的时候，通常会按值传递（值参）。这意味着函数接收的是参数的副本，原始数据不会被修改。

```c++
void func(int value); // 值参
```

- `const` 引用：当参数是对象且复制开销较大，或者想要避免不必要的复制时，会使用 `const` 引用。这样可以传递引用(地址)，函数内部不能修改引用的对象。

```c++
void func(const std::vector<int>& vec); // const引用
```

2. 非可选输出参数 或 输入/输出参数：这些参数用于从函数中输出数据，或者同时作为输入和输出。

- 引用：非可选的输出或输入/输出参数通常会使用引用，这样函数可以直接修改传入的变量的值。因为引用不能为空，所以这样的参数是必须提供的。

```c++
void func(std::string& outStr); // 输出参数，引用
void func(std::vector<int>& inOutVec); // 输入/输出参数，引用
```

3. 可选参数：这些是函数可以没有的参数，即使调用者不提供它们，函数也能正常运行。

-  `std::optional` ：对于可选的按值输入参数，可以使用 `std::optional` 来表示。 `std::optional` 可以包含一个值，也可以不包含值，提供了一种表达参数可选性的方式。

```c++
void func(std::optional<int> optValue); // 可选的按值输入
```

-  `const` 指针：对于其他类型的可选输入参数，通常使用 `const` 指针。指针可以是 `nullptr` ，这样就可以表达参数的可选性。

```c++
void func(const std::vector<int>* optVec); // 可选的输入，const指针
```

- 非常量指针：对于可选的输出参数和可选的输入/输出参数，通常使用非常量指针。非常量指针同样可以是 `nullptr` ，表示参数是可选的，且函数可以通过指针修改传入的参数。

```c++
void func(int* optOutValue); // 可选的输出或输入/输出参数，非常量指针
```

## 4.2. 编写简短函数

- 函数不超过40行。

## 4.3. 函数重载

- 如果打算重载一个函数，可以试试改在函数名里加上参数信息。例如，用 `AppendString()` 和 `AppendInt()` 等，而不是一口气重载多个 `Append()`。如果重载函数的目的是为了支持不同数量的同一类型参数，则优先考虑使用 `std::vector` 以便使用者可以用**列表初始化**指定参数。

例如，

```c++
// 使用 std::vector 作为参数
void printMessages(const std::vector<std::string>& messages) {
    for (const auto& msg : messages) {
        std::cout << msg << std::endl;
    }
}

int main() {
    // 列表初始化来指定参数，传递一个参数
    printMessages({"Hello, World!"});
    
    // 列表初始化来指定参数，传递三个参数
    printMessages({"This", "is", "Code Companion"});

    return 0;
}
```



## 4.4. 缺省参数

## 4.5. 函数返回类型后置语法

# 7. 命名约定

## 7.1. 通用命名规则

## 7.2. 文件命名

- 文件名要全部小写，可以包含下划线( `_` )或连字符( `-` )，依照项目的约定。如果没有约定，那么" `_` "更好。
- 可接受的文件命名示例:
  - `my_useful_class.cc`
  - `my-useful-class.cc`
  - `myusefulclass.cc`

## 7.3. 类型命名

- 类型名称的每个单词首写字母均大写，不包含下划线。

所有类型命名--类，结构体，类型定义( `typedef` )，枚举，类型模板参数--均使用相同约定，即以大写字母开始，每个单词首写字母均大写，不包含下划线。例如：

```c++
// 类和结构体
class UrlTable { ...
class UrlTableTester { ...
struct UrlTableProperties { ...

// 类型定义
typedef hash_map<UrlTableProperties *, string> PropertiesMap;

// using 别名
using PropertiesMap = hash_map<UrlTableProperties *, string>;

// 枚举
enum UrlTableErrors { ...
```



## 7.4. 变量命名

- 变量(包括函数参数)和数据成员名一律小写，单词之间用下划线连接。类的成员函数变量以m_开头。

**普通变量：**

```c++
string table_name;  // 好 - 用下划线.
string tablename;   // 好 - 全小写.

string tableName;  // 差 - 混合大小写
```

**类的成员变量：**

```c++
class TableInfo {
  ...
 private:
  string m_table_name;  // 好 - 后加下划线.
  string m_tablename;   // 好.
  static Pool<TableInfo>* m_pool;  // 好.
};
```



## 7.5. 常量命名

- 声明为 `constexpr` 或 `const` 的变量，或在程序运行期间其值始终保持不变的，命名时以 "k" 开头，大小写混合。例如：
- **暂时使用全大写代替**

```c++
const int kDaysInAWeek = 7;
```

- 所有具有静态存储类型的变量(例如静态变量或全局变量)都应当以此方式命名。对于其他存储类型的变量，如自动变量等，这条规则时可选的。如果不采用这条规则，就按照一般的变量命名规则。

## 7.6. 函数命名

- **常规函数使用大小写混合**, **取值**和**设值**函数则要求**与变量名匹配**: `MyExcitingFunction()`, `MyExcitingMethod()`, `my_exciting_member_variable()`, `set_my_exciting_member_variable()`.

## 7.7. 命名空间命名

- 命名空间以小写字母命名。最高级命名空间的名字取决于项目名称。要注意避免嵌套命名空间的名字之间和常见的顶级命名空间的名字发生冲突。
- 顶级命名空间的名称应当使项目名或者是该命名空间中的代码所属的团队的名字。命名空间中的代码，应当存放于和命名空间的名字匹配的文件夹或其子文件夹中。
- 不要使用缩写作为名称的规则同样适用于命名空间。命名空间中的代码极少需要设计命名空间的名称，因此没有必要在命名空间中使用缩写。

## 7.8. 枚举命名

## 7.9. 宏命名

## 7.10. 命名规则的特例