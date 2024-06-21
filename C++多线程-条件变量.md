## 1. 什么是C++条件变量

C++条件变量(condition variable)是一种线程同步机制，用于在线程之间进行通信和同步。它允许一个线程等待另一个线程满足某个条件后再继续执行。

## 2. C++条件变量的特点

- 避免忙等待：条件变量允许线程在等待条件时进入休眠状态，而不是忙等待。这样可以避免资源的浪费，提高系统性能。
- 精确的线程唤醒：使用条件变量可以实现精确的线程唤醒。线程可以在等待条件时条用`wait()`方法，等待被通知唤醒。这种方式可以确保线程在条件满足时才被唤醒，避免了虚假唤醒的问题。
- 防止竞态条件：条件变量通常与互斥锁配合使用，可以有效防止竞态条件的发生。在等待条件时，线程会先获取互斥锁，然后在条件变量上等待。这样可以确保在检查和等待条件之间的时间内，其他线程不会改变条件，从而避免了竞态条件。
- 线程间的通信：条件变量提供了一种线程间进行通信的方式。一个线程可以等待另一个线程满足某个条件后进行下一步操作，从而实现了线程间的协作。
- 灵活性：条件变量允许多个线程等待同一个条件，且在条件满足时可以选择唤醒单个线程或唤醒所有等待的线程，具有较高的灵活性。

综上所述，C++条件变量的特点包括避免忙等待、提供精确的线程唤醒机制、防止竞态条件、实现线程间的通信和灵活性。这些特点使得条件变量称为多线程编程中重要的同步和通信工具。

## 3. 如何使用C++条件变量

以下是一个简单的C++条件变量示例，

```c++
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>

std::mutex mtx;
std::condition_variable cv;
bool ready = false;

void thread_func() {
    // 模拟一些计算工作
    std::this_thread::sleep_for(std::chrono::seconds(1));

    // 工作完成后设置 ready 为 true，并通知等待的线程
    {
        std::lock_guard<std::mutex> lock(mtx);
        ready = true;
    }
    cv.notify_one(); // 通知等待的线程
}

int main() {
    std::thread t(thread_func);

    {
        std::unique_lock<std::mutex> lock(mtx);
        // 等待条件变量满足
        cv.wait(lock, []{ return ready; });
    }

    std::cout << "Thread is ready." << std::endl;

    t.join();

    return 0;
}
```

主线程等待条件变量`ready`变为`true`。而`thread_func`函数在完成工作后将`ready`设置为`true`，并通过`cv.notify_one()`通知主线程条件已满足。使用条件变量时，需要与互斥锁配合使用，以避免竞态条件。通常情况下，等待条件时，会先获取互斥锁，然后在条件变量上等待，直到条件满足。

## 4. 使用C++条件变量需要注意的地方

- 与互斥锁配合使用：条件变量通常与互斥锁(`mutex`)结合使用。在等待条件时，线程需要先获取互斥锁，然后在体哦阿健变量上等待，这样可以避免竞态条件的发生。
- 考虑虚假唤醒：在等待条件时，要考虑到可能出现虚假唤醒(spurious wakeup)的情况。即使没用调用`notify_one()`或`notify_all()`，线程有时也可能被唤醒。因此，在条件满足后，需要重新检查条件是否真的满足，以防止虚假唤醒导致的逻辑错误。
- `notify_one()`和`notify_all()`：在满足条件时，可以选择使用`notify_one()`来唤醒等待队列中的另一个线程，或者使用`notify_all()`来唤醒所有等待的线程。根据具体的需求和性能考虑来选择合适的唤醒方式。

## 5.

```c++
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <deque>
#include <chrono>

std::mutex g_cvMutex;
std::condition_variable g_cv;

//缓存区
std::deque<int> g_data_deque;
//缓存区最大数目
const int  MAX_NUM = 30;
//数据
int g_next_index = 0;

//生产者，消费者线程个数
const int PRODUCER_THREAD_NUM = 3;
const int CONSUMER_THREAD_NUM = 3;

void  producer_thread(int thread_id)
{
     while (true)
     {
         std::this_thread::sleep_for(std::chrono::milliseconds(500));
         //加锁
         std::unique_lock <std::mutex> lk(g_cvMutex);
         //当队列未满时，继续添加数据
         g_cv.wait(lk, [](){ return g_data_deque.size() <= MAX_NUM; });
         g_next_index++;
         g_data_deque.push_back(g_next_index);
         std::cout << "producer_thread: " << thread_id << " producer data: " << g_next_index;
         std::cout << " queue size: " << g_data_deque.size() << std::endl;
         //唤醒其他线程 
         g_cv.notify_all();
         //自动释放锁
     }
}

void  consumer_thread(int thread_id)
{
    while (true)
    {
        std::this_thread::sleep_for(std::chrono::milliseconds(550));
        //加锁
        std::unique_lock <std::mutex> lk(g_cvMutex);
        //检测条件是否达成
        g_cv.wait( lk,   []{ return !g_data_deque.empty(); });
        //互斥操作，消息数据
        int data = g_data_deque.front();
        g_data_deque.pop_front();
        std::cout << "\tconsumer_thread: " << thread_id << " consumer data: ";
        std::cout << data << " deque size: " << g_data_deque.size() << std::endl;
        //唤醒其他线程
        g_cv.notify_all();
        //自动释放锁
    }
}


int main()
{
    std::thread arrRroducerThread[PRODUCER_THREAD_NUM];
    std::thread arrConsumerThread[CONSUMER_THREAD_NUM];

    for (int i = 0; i < PRODUCER_THREAD_NUM; i++)
    {
        arrRroducerThread[i] = std::thread(producer_thread, i);
    }

    for (int i = 0; i < CONSUMER_THREAD_NUM; i++)
    {
        arrConsumerThread[i] = std::thread(consumer_thread, i);
    }

    for (int i = 0; i < PRODUCER_THREAD_NUM; i++)
    {
        arrRroducerThread[i].join();
    }

    for (int i = 0; i < CONSUMER_THREAD_NUM; i++)
    {
        arrConsumerThread[i].join();
    }
    
    return 0;
}
```

