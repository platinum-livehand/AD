## CMake 中的链接权限	

​	在 CMake 中，链接权限决定了库在何时被链接到目标上。主要有三种权限：

- **PRIVATE**(私有)：当目标自身需要此链接库时使用。
- **PUBLIC**(公有)：当目标自身或其他目标链接了这个目标时使用。
- **INTERFACE**(接口)：当目标自身不需要此链接库，但其他目标链接了这个目标时使用。

```cmake
target_link_libraries(target PRIVATE lib1)
target_link_libraries(target PUBLIC lib2)
target_link_libraries(target INTERFACE lib3)
```

| 权限      | 定义                                                   | 使用场景                                                    | 与其他权限的比较   |
| --------- | ------------------------------------------------------ | ----------------------------------------------------------- | ------------------ |
| PRIVATE   | 只有目标自身需要此链接库时使用                         | 不希望外部继承的依赖                                        | 不会被其他目标继承 |
| PUBLIC    | 目标自身或其他目标链接了这个目标时使用                 | 希望被其他目标继承的依赖                                    | 会被其他目标继承   |
| INTERFACE | 目标自身不需要此链接库，但其他目标链接了这个目标时使用 | header-only的头文件库，或者只希望传递依赖而不实际链接的情况 | 只会被其他目标继承 |

## INTERFACE 应用场景

- **模块化设计**：将公共链接库抽象到 INTERFACE 库中，使得多个目标可以共享这些链接库。

### CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(ModularDesignExample)

# 定义一个INTERFACE库，用于共享公共链接库
add_library(common_libs INTERFACE)

# 设置INTERFACE库的链接库（假设这些库是共享的公共库）
target_link_libraries(common_libs INTERFACE pthread m)

# 定义第一个可执行目标
add_executable(app1 main1.cpp)
# 将INTERFACE库链接到第一个可执行目标
target_link_libraries(app1 PRIVATE common_libs)

# 定义第二个可执行目标
add_executable(app2 main2.cpp)
# 将INTERFACE库链接到第二个可执行目标
target_link_libraries(app2 PRIVATE common_libs)
```

### main1.cpp

```c++
#include <iostream>
#include <pthread.h>

int main() {
    std::cout << "Hello from app1!" << std::endl;
    // 使用pthread库的代码
    return 0;
}
```

### main2.cpp

```c++
#include <iostream>
#include <pthread.h>

int main() {
    std::cout << "Hello from app2!" << std::endl;
    // 使用pthread库的代码
    return 0;
}
```

​	在这个示例中，`common_libs` 是一个 INTERFACE 库，包含了公共链接库 `pthread` 和 `m`。`app1` 和 `app2` 两个可执行目标通过链接到 `common_libs` 来共享这些链接库。

- **头文件依赖库**：对于 header-only 库，可以使用 INTERFACE 库来管理其依赖的链接库。**因为 header-only 库一般为 模板库，或者只在调用处编译，自己本身并不编译生成二进制文件。**

### CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(HeaderOnlyLibraryExample)

# 查找Boost库
find_package(Boost REQUIRED)

# 定义一个INTERFACE库，用于头文件库
add_library(math_utils INTERFACE)

# 设置头文件库的包含目录
target_include_directories(math_utils INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/include)

# 设置INTERFACE库的链接库
target_link_libraries(math_utils INTERFACE Boost::Boost)

# 定义一个可执行目标
add_executable(app main.cpp)

# 将INTERFACE库链接到可执行目标
target_link_libraries(app PRIVATE math_utils)
```

### include/math_utils.h

```c++
#ifndef MATH_UTILS_H
#define MATH_UTILS_H

#include <boost/math/constants/constants.hpp>

inline double pi() {
    return boost::math::constants::pi<double>();
}

#endif // MATH_UTILS_H
```

### main.cpp

```c++
#include <iostream>
#include "math_utils.h"

int main() {
    std::cout << "Pi is: " << pi() << std::endl;
    return 0;
}
```

​	这个示例中，`math_utils` 是一个头文件库(header-only library)，它依赖于 `Boost` 库。通过使用 INTERFACE 库，我们将头文件库的包含目录和链接库信息传递给依赖它的执行目标 `app`。

- **编译选项管理**：通过 INTERFACE 库几种管理链接选项，使得项目的构建配置更加简洁和易于维护。