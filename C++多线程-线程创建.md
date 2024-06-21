C++多线程编程是指在C++程序中利用多线程技术，使程序能够同时执行多个任务，从而提高程序的执行效率和响应速度。线程是C++程序实现并行执行的最小执行单元。C++标准库从C++11开始引入了线程支持，主要通过包含`<thread>`头文件来实现。

## 1. 几种线程创建的方式

### 1.1  使用`std::thread`直接创建线程

这是最直接的方式，通过实例化std::thread对象并传入希望在线程中执行的函数或者可调用对象(如lambda表达式)。

```c++
#include <iostream>
#include <thread>

void threadFunction() {
    std::cout << "Hello, World from thread!" << std::endl;
}

int main() {
    std::thread myThread(threadFunction); // 创建线程并执行threadFunction
    myThread.join(); // 等待线程结束
    return 0;
}
```

### 1.2 使用`lambda`直接创建线程

Lambda表达式可以捕获外部变量，提供了一种灵活的方式来定义线程执行的代码块。

```c++
#include <iostream>
#include <thread>

int main() {
    int x = 10;
    std::thread myThread([&]() { // 使用捕获列表捕获x
        std::cout << "Hello from lambda thread, x = " << x << std::endl;
    });
    myThread.join();
    return 0;
}
```

### 1.3 传递成员函数作为线程的入口

如果需要在线程中执行某个对象的成员函数，可以使用`std::mem_fn`或直接使用`lambda`表达式来绑定对象和成员函数。

```c++
#include <iostream>
#include <thread>
#include <functional> // for std::mem_fn

class MyClass {
public:
    void threadMemberFunction() {
        std::cout << "Hello from member function thread!" << std::endl;
    }
};

int main() {
    MyClass obj;
    
    // 方法一：使用std::mem_fn
    std::thread myThread(std::mem_fn(&MyClass::threadMemberFunction), &obj);
    
    // 方法二：使用lambda表达式
    // std::thread myThread([&](){ obj.threadMemberFunction(); });
    
    myThread.join();
    return 0;
}
```

### 1.4 传递函数对象或者`functor`

C++中的函数对象(`functor`，即重载了`operator()`的对象)也可以作为线程的入口点。

```c++
#include <iostream>
#include <thread>

struct Functor {
    void operator()() const {
        std::cout << "Hello from functor thread!" << std::endl;
    }
};

int main() {
    std::thread myThread(Functor{});
    myThread.join();
    return 0;
}
```

### 1.5 使用`std::async`开启异步任务

`std::async`是C++11引入的另一个用于异步执行函数的工具，虽然它并不直接创建线程，但可以用来启动一个任务并在后台异步执行，其结果通过`std::future`或`std::shared_future`来获取。`std::async`提供了比直接使用`std::thread`更高级的抽象，支持任务的启动策略(比如决定是否立即执行)和结果的获取方式。

```c++
#include <iostream>
#include <future>
#include <thread>
#include <chrono>

int calculateSum(int x, int y) {
    std::this_thread::sleep_for(std::chrono::seconds(1)); // 模拟耗时操作
    return x + y;
}

int main() {
    std::future<int> result = std::async(std::launch::async, calculateSum, 10, 20);
    std::cout << "Calculating sum..." << std::endl;
    
    // 主线程可以继续执行其他任务...
    
    // 获取结果（此行可能会阻塞直到结果可用）
    int sum = result.get();
    std::cout << "Sum is: " << sum << std::endl;
    
    return 0;
}
```

如果不关心异步执行的结果，可以忽略返回的`std::future`对象：

```c++
std::async(std::launch::async, [](){
    // 执行一些操作
    std::cout << "Async task executed." << std::endl;
});
```