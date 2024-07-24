## vcpkg

```shell
PS C:\vcpkg> vcpkg install opencv2:x64-mingw-static
Computing installation plan...
The following packages are already installed:
    opencv2[core,tiff,png,jpeg,eigen,default-features]:x64-mingw-static@2.4.13.7#20
opencv2:x64-mingw-static is already installed
Total install time: 123 us
The package opencv2 is compatible with CMake if you set the OpenCV_DIR *before* the find_package call

    set(OpenCV_DIR "${VCPKG_INSTALLED_DIR}/x64-mingw-static/share/opencv2")
    find_package(OpenCV REQUIRED)
```

​	安装 OpenCV 的时候会同时安装依赖的其他库。

```shell
PS C:\vcpkg> vcpkg list
libjpeg-turbo:x64-mingw-static                    3.0.2#1             libjpeg-turbo is a JPEG image codec that uses SI...
liblzma:x64-mingw-static                          5.6.2               Compression library with an API similar to that ...
libpng:x64-mingw-static                           1.6.43#2            libpng is a library implementing an interface fo...
opencv2:x64-mingw-static                          2.4.13.7#20         Open Source Computer Vision Library
opencv2[default-features]:x64-mingw-static                            Platform-dependent default features
opencv2[eigen]:x64-mingw-static                                       Eigen support for opencv
opencv2[jpeg]:x64-mingw-static                                        JPEG support for opencv
opencv2[png]:x64-mingw-static                                         PNG support for opencv
opencv2[tiff]:x64-mingw-static                                        TIFF support for opencv
tiff:x64-mingw-static                             4.6.0#4             A library that supports the manipulation of TIFF...
tiff[jpeg]:x64-mingw-static                                           Support JPEG compression in TIFF image files
tiff[lzma]:x64-mingw-static                                           Support LZMA compression in TIFF image files
tiff[zip]:x64-mingw-static                                            Support ZIP/deflate compression in TIFF image files
vcpkg-cmake-config:x64-windows                    2024-05-23
vcpkg-cmake:x64-windows                           2024-04-23
vcpkg-get-python-packages:x64-windows             2024-01-24
zlib:x64-mingw-static                             1.3.1               A compression library
```

## CMakeLists.txt

```cmake
# windows第一次执行 cmake .. -G "MinGW Makefiles"
cmake_minimum_required(VERSION 3.29.0)

# 项目名称
project(cmake_test)

# 设置Vcpkg工具链路径
set(CMAKE_TOOLCHAIN_FILE "C:/vcpkg/scripts/buildsystems/vcpkg.cmake")

# 检测操作系统
if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
    message(STATUS "Configuring on/for Linux")
elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
    message(STATUS "Configuring on/for macOS")
elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows")
    message(STATUS "Configuring on/for Windows")
elseif(CMAKE_SYSTEM_NAME STREQUAL "AIX")
    message(STATUS "Configuring on/for IBM AIX")
else()
    message(STATUS "Configuring on/for ${CMAKE_SYSTEM_NAME}")
endif()

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
message(STATUS "CXX_STANDARD = ${CMAKE_CXX_STANDARD}")

# 设置输出目录
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)

# 添加源文件目录
aux_source_directory(${CMAKE_SOURCE_DIR}/src SRC_LIST)

# 添加头文件目录
include_directories(${CMAKE_SOURCE_DIR}/include)

set(TIFF_INCLUDE_DIR "C:/vcpkg/packages/tiff_x64-mingw-static/include")
set(TIFF_LIBRARY "C:/vcpkg/packages/tiff_x64-mingw-static/lib/libtiff.a")

set(ZLIB_INCLUDE_DIR "C:/vcpkg/packages/zlib_x64-mingw-static/include")
set(ZLIB_LIBRARY "C:/vcpkg/packages/zlib_x64-mingw-static/lib/libzlib.a")

set(PNG_PNG_INCLUDE_DIR "C:/vcpkg/packages/libpng_x64-mingw-static/include")
set(PNG_LIBRARY "C:/vcpkg/packages/libpng_x64-mingw-static/lib/libpng.a")

set(JPEG_INCLUDE_DIR "C:/vcpkg/packages/libjpeg-turbo_x64-mingw-static/include")
set(JPEG_LIBRARY "C:/vcpkg/packages/libjpeg-turbo_x64-mingw-static/lib/libjpeg.a")

set(OpenCV_DIR "C:/vcpkg/packages/opencv2_x64-mingw-static/share/opencv2")
find_package(OpenCV REQUIRED)

# 查找JPEG库
find_package(JPEG REQUIRED)

# 生成可执行程序
add_executable(${PROJECT_NAME} ${SRC_LIST} main.cpp)

# 可执行程序包含 EasyX 库的头文件
target_include_directories(${PROJECT_NAME} 
PUBLIC 
${CMAKE_SOURCE_DIR}/EasyX/include
${OpenCV_INCLUDE_DIRS})

# 可执行程序链接 EasyX 库
target_link_libraries(${PROJECT_NAME} 
PUBLIC
${CMAKE_SOURCE_DIR}/EasyX/lib64/libeasyx.a
JPEG::JPEG
${OpenCV_LIBS})
```

## main.cpp(卡尔曼滤波)

```c++
#include <opencv2/opencv.hpp>
#include <iostream>

int main() {
    // 创建卡尔曼滤波器对象，状态向量维度为4（位置x, 位置y, 速度x, 速度y），测量向量维度为2（位置x, 位置y）
    cv::KalmanFilter kf(4, 2, 0);

    // 初始化状态转移矩阵 A
    kf.transitionMatrix = (cv::Mat_<float>(4, 4) << 
                           1, 0, 1, 0, 
                           0, 1, 0, 1, 
                           0, 0, 1, 0, 
                           0, 0, 0, 1);

    // 初始化测量矩阵 H
    kf.measurementMatrix = (cv::Mat_<float>(2, 4) << 
                            1, 0, 0, 0, 
                            0, 1, 0, 0);

    // 初始化过程噪声协方差矩阵 Q
    kf.processNoiseCov = (cv::Mat_<float>(4, 4) << 
                          1, 0, 0, 0, 
                          0, 1, 0, 0, 
                          0, 0, 1, 0, 
                          0, 0, 0, 1) * 1e-4;

    // 初始化测量噪声协方差矩阵 R
    kf.measurementNoiseCov = (cv::Mat_<float>(2, 2) << 
                              1, 0, 
                              0, 1) * 1e-1;

    // 初始化误差协方差矩阵 P
    kf.errorCovPost = (cv::Mat_<float>(4, 4) << 
                       1, 0, 0, 0, 
                       0, 1, 0, 0, 
                       0, 0, 1, 0, 
                       0, 0, 0, 1);

    // 初始状态
    kf.statePost = (cv::Mat_<float>(4, 1) << 
                    0, 
                    0, 
                    0, 
                    0);

    cv::Mat measurement = cv::Mat::zeros(2, 1, CV_32F);

    // 创建窗口
    cv::namedWindow("Kalman Filter", cv::WINDOW_AUTOSIZE);

    while (true) {
        // 生成模拟测量值（这里我们假设目标沿对角线移动）
        measurement.at<float>(0) = 0.5f + 1.0f * rand() / RAND_MAX;
        measurement.at<float>(1) = 0.5f + 1.0f * rand() / RAND_MAX;

        // 卡尔曼滤波预测
        cv::Mat prediction = kf.predict();

        // 更新卡尔曼滤波器
        cv::Mat estimated = kf.correct(measurement);

        // 显示测量值和估计值
        std::cout << "Measurement: [" << measurement.at<float>(0) << ", " << measurement.at<float>(1) << "]" << std::endl;
        std::cout << "Estimated: [" << estimated.at<float>(0) << ", " << estimated.at<float>(1) << "]" << std::endl;

        // 可视化
        cv::Mat img(500, 500, CV_8UC3, cv::Scalar::all(0));
        cv::circle(img, cv::Point(measurement.at<float>(0) * 500, measurement.at<float>(1) * 500), 5, cv::Scalar(0, 0, 255), -1);
        cv::circle(img, cv::Point(estimated.at<float>(0) * 500, estimated.at<float>(1) * 500), 5, cv::Scalar(0, 255, 0), -1);
        cv::imshow("Kalman Filter", img);

        // 按下 'q' 键退出
        if (cv::waitKey(100) == 'q') {
            break;
        }
    }

    return 0;
}
```

## 运行结果和项目结构

![](..\picture\CMake_添加OpenCV.png)