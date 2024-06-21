​	在C++中，多线程编程时为了避免**数据竞争**和**并发**问题，确保线程安全，经常需要使用**线程锁来同步对共享资源的访问**。C++11标准库提供了多种类型的线程锁，包括**互斥锁**、**递归锁**、**读写锁**等。下面简要介绍几种常用的线程锁及其使用方法：

## 1. 互斥锁(`std::mutex`)

​	互斥锁是最基本的线程同步机制，一次只允许一个线程访问被保护的资源。

```c++
#include <mutex>
#include <iostream>
#include <thread>

std::mutex mtx; // 全局或静态的互斥锁

void printMessage(const std::string& message) {
    std::lock_guard<std::mutex> lock(mtx); // 自动加锁并在作用域结束时解锁
    std::cout << message << std::endl;
}

int main() {
    std::thread t1(printMessage, "Hello");
    std::thread t2(printMessage, "World");

    t1.join();
    t2.join();

    return 0;
}
```

## 2. 递归锁(`std::recursive_mutex`)

递归锁允许同一个线程多次锁定同一个锁，这对于需要在递归函数或者嵌套调用中锁定同一资源的场景非常有用。

```c++
std::recursive_mutex recursive_mtx;

void recursiveFunction(int depth) {
    recursive_mtx.lock();
    std::cout << "Recursion depth: " << depth << std::endl;
    if (depth > 0) {
        recursiveFunction(depth - 1);
    }
    recursive_mtx.unlock();
}
```

## 3. 读写锁(`std::shared_mutex` / `std::shared_timed_mutex`)

读写锁允许多个读者同时访问资源，但写者访问时会独占资源，阻止所有读者和写者。这对于读多写少的场景非常高效。

```c++
std::shared_mutex rw_mtx;

void readerFunction() {
    std::shared_lock<std::shared_mutex> readLock(rw_mtx);
    // 读取共享资源
}

void writerFunction() {
    std::unique_lock<std::shared_mutex> writeLock(rw_mtx);
    // 修改共享资源
}
```

## 4. 简化锁操作(`std::lock_guard`)

`std::lock_guard`是C++标准库中`<mutex>`头文件提供的一种RAII(Resource Acquisition Is Initialization)类，用于简化互斥锁(如`std::mutex)`的管理和使用，确保锁的自动释放，即使在异常情况下也能安全释放锁资源，防止死锁的发生。

```c++
#include <mutex>
#include <iostream>
#include <thread>

std::mutex mtx;

void printMessage(const std::string& message) {
    std::lock_guard<std::mutex> lock(mtx); // 锁定互斥锁
    std::cout << message << std::endl;
    // 当离开该作用域时，lock对象被销毁，自动调用unlock()
}

int main() {
    std::thread t1(printMessage, "Hello");
    std::thread t2(printMessage, "World");

    t1.join();
    t2.join();

    return 0;
}
```

## 5. 简化锁操作(`std::unique_lock`)

`std::unique_lock`是C++标准库中的一个模板类，位于`<mutex>`头文件中，它提供了比`std::lock_guard`更灵活的互斥锁管理方式。与`std::lock_guard`一样，`std::unique_lock`也采用了RAII(Resource Acquisition Is Initialization)技术来确保锁的自动释放，但提供了更多控制选项，使得它在更广泛的并发编程场景中更加使用。

```c++
#include <mutex>
#include <iostream>
#include <thread>
#include <condition_variable>

std::mutex mtx;
std::condition_variable cv;

void waitForSignal() {
    std::unique_lock<std::mutex> lock(mtx);
    std::cout << "Waiting for signal...\n";
    cv.wait(lock); // 会自动释放锁，然后等待条件变量，条件满足时重新锁定
    std::cout << "Signal received.\n";
}

void sendSignal() {
    std::this_thread::sleep_for(std::chrono::seconds(2)); // 模拟延时
    {
        std::lock_guard<std::mutex> lock(mtx);
        cv.notify_one(); // 唤醒一个等待的线程
    }
    std::cout << "Signal sent.\n";
}

int main() {
    std::thread t1(waitForSignal);
    std::thread t2(sendSignal);

    t1.join();
    t2.join();

    return 0;
}
```

## 6. 两种简化锁操作的区别

- 自动锁定：`std::lock_guard`在构造时立即锁定互斥锁，并在析构时解锁，适用用于简单的锁定需求。
- 灵活性：`std::unique_lock`提供了更多的控制选项，可以手动锁定和解锁，更适合复杂同步控制和与条件变量的配合使用。
- 转移性：`std::unique_lock`可以通过移动构造函数转移所有权，而`std::lock_guard`则不行。