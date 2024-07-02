​	C++11 引入了 `<chrono>` 库，它提供了精确的时间和日期处理功能。以下是一些常见操作及示例，涵盖从获取当前时间到计算时间差和格式化输出等。

## 获取当前时间

​	使用 `std::chrono::system_clock` 获取当前时间点。

```c++
#include <iostream>
#include <chrono>

int main() {
    // 返回类型为 std::chrono::time_point，用于记录当前的时间点
    auto now = std::chrono::system_clock::now();
    // time_t 日历时间(Calendar Time)(s) 1719883195
    std::time_t now_time = std::chrono::system_clock::to_time_t(now);
    std::cout << "UTC time: " << now_time << std::endl; 
    // ctime() 返回 char* 格式的日期 Tue Jul 02 09:19:55 2024
    std::cout << "Current time: " << std::ctime(&now_time);
    return 0;
}
```

## 计算时间差

​	计算两个时间点之间的差值

```c++
#include <iostream>
#include <chrono>
#include <thread>

int main() {
    auto start = std::chrono::high_resolution_clock::now();

    // 模拟一些工作
    std::this_thread::sleep_for(std::chrono::seconds(3));

    auto end = std::chrono::high_resolution_clock::now();
    std::chrono::duration<double> elapsed = end - start;

    std::cout << "Elapsed time: " << elapsed.count() << " seconds\n";
    return 0;
}
```

## 获取时间点的时间单位

​	获取时间点特定的时间单位。

```c++
#include <iostream>
#include <chrono>

int main() {
    auto now = std::chrono::system_clock::now();
    auto duration = now.time_since_epoch();

    auto millis = std::chrono::duration_cast<std::chrono::milliseconds>(duration).count();
    auto micros = std::chrono::duration_cast<std::chrono::microseconds>(duration).count();
    auto nanos = std::chrono::duration_cast<std::chrono::nanoseconds>(duration).count();

    std::cout << "Milliseconds since epoch: " << millis << "\n";
    std::cout << "Microseconds since epoch: " << micros << "\n";
    std::cout << "Nanoseconds since epoch: " << nanos << "\n";

    return 0;
}
```

## 使用不同的时钟

​	不同功能的时钟。

```c++
#include <iostream>
#include <chrono>
#include <thread>

int main() {
    // 用于记录当前的日期和时间，生成事件的时间戳等。
    auto system_start = std::chrono::system_clock::now();
    // 用于测量性能基准的时间间隔、代码执行时间的分析等
    auto steady_start = std::chrono::steady_clock::now();
    // 用于高精度地测量非常短的时间间隔，例如在科学实验或高性能计算中
    auto highres_start = std::chrono::high_resolution_clock::now();

    std::this_thread::sleep_for(std::chrono::seconds(1));

    auto system_end = std::chrono::system_clock::now();
    auto steady_end = std::chrono::steady_clock::now();
    auto highres_end = std::chrono::high_resolution_clock::now();

    std::chrono::duration<double> system_elapsed = system_end - system_start;
    std::chrono::duration<double> steady_elapsed = steady_end - steady_start;
    std::chrono::duration<double> highres_elapsed = highres_end - highres_start;

    std::cout << "system_clock elapsed time: " << system_elapsed.count() << " seconds\n";
    std::cout << "steady_clock elapsed time: " << steady_elapsed.count() << " seconds\n";
    std::cout << "high_resolution_clock elapsed time: " << highres_elapsed.count() << " seconds\n";

    return 0;
}
```

## 等待时间

​	使用 `<chrono>` 和 `std::this_thread::sleepfor` 实现线程休眠。

```c++
#include <iostream>
#include <chrono>
#include <thread>

int main() {
    std::cout << "Waiting for 3 seconds..." << std::endl;
    std::this_thread::sleep_for(std::chrono::seconds(3));
    std::cout << "Done!" << std::endl;
    return 0;
}
```

## 时间点的算术运算

​	对时间点进行加减操作。

```c++
#include <iostream>
#include <chrono>

int main() {
    auto start = std::chrono::system_clock::now();
    // 时间点加一分钟
    auto later = start + std::chrono::minutes(1);

    std::time_t start_time = std::chrono::system_clock::to_time_t(start);
    std::time_t later_time = std::chrono::system_clock::to_time_t(later);

    std::cout << "Start time: " << std::ctime(&start_time);
    std::cout << "Later time (1 minute later): " << std::ctime(&later_time);

    return 0;
}
```

## 时间单位之间的转换

​	将不同时间单位进行转换。

```c++
#include <iostream>
#include <chrono>

int main() {
    std::chrono::minutes mins(3);
    // 将 3 分钟 转换为 180 秒
    std::chrono::seconds secs = std::chrono::duration_cast<std::chrono::seconds>(mins);

    std::cout << "3 minutes is " << secs.count() << " seconds\n";
    return 0;
}
```

## 格式化输出当前日期和时间

​	转化成想要的格式。

```c++
#include <iostream>
#include <chrono>
#include <iomanip>
#include <ctime>

int main() {
    auto now = std::chrono::system_clock::now();
    std::time_t now_time = std::chrono::system_clock::to_time_t(now);

    std::cout << "Current time: " << std::put_time(std::localtime(&now_time), "%Y-%m-%d %H:%M:%S") << '\n';
    return 0;
}
```

