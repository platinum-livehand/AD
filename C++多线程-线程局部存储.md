​	`thread_local`是C++11引入的关键字，用于声明变量具有线程局部存储期。这意味着每个线程都拥有此变量的一个独立实例，每个线程只能访问自己的实例，不用线程间的实例互不影响。这对于避免多线程编程中的数据竞争和同步开销非常有用，特别是当每个线程需要自己的状态或缓存时。

## 基本语法

```c++
// thread_local 数据类型 变量名 [= 初始化值];
thread_local int threadId = std::this_thread::get_id();
```

## 特点

1. **独立性：**每个线程看到的是该变量的不同实例，即使多个线程声明了相同的`thread_local`变量，它们也是互不干扰的。
2. **生命周期：**`thread_local`变量的生命周期与所属线程相同，当线程结束时，相关联的`thread_local`变量会被自动销毁。
3. **初始化：**每个线程首次遇到`thread_local`变量声明时，如果该变量有初始化器，则会执行初始化。这意味着即使在多线程中，每个线程也会独立的执行初始化。
4. **存储：**`thread_local`变量通常存储在每个线程的堆栈上，或者是由线程管理器分配的内存区域，这取决于编译器和平台实现。

## 用途

- **避免数据竞争：**在多线程程序中，使用thread_local变量可以避免因共享数据而产生的竞态条件，因为每个线程都有自己的副本。
- **缓存优化：**每个线程可以由自己的缓存，减少对共享资源的竞争和同步开销。
- **线程特定数据：**存储线程ID、线程特定的日志句柄、线程私有的统计信息等。

## 示例

​	下面是一个简单的线程安全的计数器类，用于统计每个线程调用某个方法的次数。这个例子展示了如何在类中使用`thread_local`变量来存储每个线程独有的计数，从而避免了多线程环境下的数据竞争问题。

```c++
#include <iostream>
#include <thread>
#include <mutex>

std::mutex mtx;

class ThreadSafeCounter {
public:
    // 增加计数的方法
    void increment() {
        ++counter_; // 使用thread_local变量
    }

    // 获取当前线程的计数
    int getCount() const {
        return counter_;
    }

private:
    // 每个线程独有的计数器
    thread_local static int counter_;
};

// 初始化静态成员变量
thread_local int ThreadSafeCounter::counter_ = 0;

// 线程函数
void worker(ThreadSafeCounter& counter, int id) {

    if(id == 1)
    {
        for (int i = 0; i < 100000; ++i) 
        {
            counter.increment();
        }
    }

    if(id == 2)
    {
        for (int i = 0; i < 1000; ++i) 
        {
            counter.increment();
        }
    }

    mtx.lock();
    std::cout << "Thread " << id << " finished. Count: " << counter.getCount() << std::endl;
    mtx.unlock();
}

int main() {
    ThreadSafeCounter counter;

    // 创建并启动两个线程
    std::thread t1(worker, std::ref(counter), 1);
    std::thread t2(worker, std::ref(counter), 2);

    t1.join();
    t2.join();

    // 注意：主函数不会看到线程内的计数，因为是线程局部的
    std::cout << "Main thread sees counter as: " << counter.getCount() << std::endl; // 总是0，因为主线程没有调用increment()

    return 0;
}
```

## 使用`thread_local static`：

- 当你使用`thread_local static`时，不同线程中的`counter_`变量实例是相互独立的，但是在类的所有实例之间，对于任何特定的线程，`counter_`是共享的。这意味着如果你创建了两个`ThreadSafeCounter`对象并在同一个线程中使用它们，它们将共享同一个`counter_`实例。
- 在你的当前实现中，由于`counter_`是`static`的，即使你创建了多个`ThreadSafeCounter`对象，每个线程对于这个类的`counter_`访问都是相同的实例。

## 不使用`static`，只使用`thread_local`：

- 如果你去掉`static`关键字，使之只有`thread_local`，那么每个线程中的每个`ThreadSafeCounter`对象都将拥有自己独立的`counter_`副本。这意味着在同一个线程中不同`ThreadSafeCounter`对象的`counter_`互不影响。
- 这种情况下，如果你的应用逻辑确实需要每个对象在每个线程中都有其独立的计数器，则移除`static`是合理的。