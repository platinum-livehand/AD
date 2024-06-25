## 信号量

​	在C++中，信号量是一种用于多线程或者多进程之间进行同步和互斥的机制。它允许线程在共享资源上进行协调，以避免竞态条件(Race Condition)等并发问题。信号量通常用于解决生产者-消费者问题，或者在有限资源的情况下协调多个线程对资源的访问。信号量通常有两种类型：

## 计数信号量(Counting Semaphore)

​	计数信号量维护一个整数计数器，该计数器可以增加或减少。线程可以通过增加或减少计数器的值来申请或释放资源。当计数器为正时，表示可用资源的数量；当计数器为零时，表示资源已被占用，线程需要等待。计数信号量常用于实现有限缓冲区等场景。

```c++
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>

std::mutex mtx;

class CountingSemaphore {
public:
    CountingSemaphore(int count = 0) : count_(count) {}

    void notify() {
        std::unique_lock<std::mutex> lock(mutex_);
        ++count_;
        condition_.notify_one();
    }

    void wait() {
        std::unique_lock<std::mutex> lock(mutex_);
        condition_.wait(lock, [this]() { return count_ > 0; });
        --count_;
    }

private:
    std::mutex mutex_;
    std::condition_variable condition_;
    int count_;
};

// 一个简单的示例，有三个线程同时访问一个共享资源
void sharedResourceAccess(CountingSemaphore& sem) {
    // 等待信号量
    sem.wait();

    // 访问共享资源
    mtx.lock();
    std::cout << "Thread " << std::this_thread::get_id() << " is accessing the shared resource." << std::endl;
    mtx.unlock();

    // 模拟共享资源的使用
    std::this_thread::sleep_for(std::chrono::seconds(1));

    // 释放信号量
    sem.notify();
}

int main() {
    const int numThreads = 8;
    CountingSemaphore semaphore(2); // 初始计数器设置为线程数量

    std::thread threads[numThreads];
    for (int i = 0; i < numThreads; ++i) {
        threads[i] = std::thread(sharedResourceAccess, std::ref(semaphore));
    }

    for (int i = 0; i < numThreads; ++i) {
        threads[i].join();
    }

    return 0;
}
```

## 二进制信号量(Binary Semaphore)

​	二进制信号量只有两种状态，通常表示资源是否可用。它可以被用于互斥访问共享资源，类似于互斥锁。当二进制信号量的值为 1 时，表示资源可用；当值为 0 时，表示资源不可用，线程需要等待。二进制信号量常用于实现临界区保护等场景。

```c++
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>

class BinarySemaphore {
public:
    BinarySemaphore(bool initialState = false) : isAvailable_(initialState) {}

    void acquire() {
        std::unique_lock<std::mutex> lock(mutex_);
        condition_.wait(lock, [this]() { return isAvailable_; });
        isAvailable_ = false; // 资源被占用
    }

    void release() {
        std::unique_lock<std::mutex> lock(mutex_);
        isAvailable_ = true; // 资源可用
        condition_.notify_one();
    }

private:
    std::mutex mutex_;
    std::condition_variable condition_;
    bool isAvailable_;
};

// 一个简单的示例，模拟两个线程竞争访问一个共享资源
void sharedResourceAccess(BinarySemaphore& sem) {
    // 获取信号量
    sem.acquire();

    // 访问共享资源
    std::cout << "Thread " << std::this_thread::get_id() << " is accessing the shared resource." << std::endl;

    // 模拟共享资源的使用
    std::this_thread::sleep_for(std::chrono::seconds(1));

    // 释放信号量
    sem.release();
}

int main() {

    const int numThreads = 8;
    BinarySemaphore semaphore(true); // 初始状态设置为资源可用

    std::thread threads[numThreads];

    for(int i; i < numThreads; ++i)
    {
        threads[i] = std::thread(sharedResourceAccess, std::ref(semaphore));
    }

    for (int i = 0; i < numThreads; ++i)
    {
        threads[i].join();
    }

    return 0;
}
```

## 总结

计数信号量(Counting Semaphore)和二进制信号量(Binary Semaphore)各自适用于不用的多线程同步场景：

**计数信号量的使用场景：**

- 有限资源的管理：当需要限制多个线程对有限资源的访问时，计数信号量可以用于控制资源的数量。
- 生产者-消费者问题：在生产者消费者模型中，计数信号量可以用于控制生产者和消费者之间的同步，确保不会出现资源的溢出或者对访问未生产的数据。
- 线程池：在线程池中，计数信号量可以用于限制并发执行的任务数量，避免资源耗尽或者性能下降。

**二进制信号量的使用场景：**

- 互斥访问共享资源：在需要确保同一时刻只有一个线程可以访问共享资源的场景中，二进制信号量可以用于实现互斥访问，防止数据竞争和数据损坏。
- 临界区保护：在涉及到临界区的情况下，可以使用二进制信号量来保护临界区，确保同时只有一个线程可以执行临界区的代码。
- 同步操作：在需要确保某些操作的顺序执行时，可以使用二进制信号量来实现线程之间的同步，例如在多线程环境中实现同步的初始化操作。

总的来说，计数信号量适用于管理有限资源和控制并发数量的场景，而二进制信号量适用于实现互斥访问共享资源和同步操作的场景。

​	
