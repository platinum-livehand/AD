## 加法运算符

```c++
int a = 5, b = 3;
int result = a + b; // result = 8
```

## 减法运算符

```c++
int a = 5, b = 3;
int result = a - b; // result = 2
```

## 乘法运算符

```c++
int a = 5, b = 3;
int result = a * b; // result = 15
```

## 除法运算符

```c++
int a = 6, b = 3;
int result = a / b; // result = 2
```

## 取模运算符

```c++
int a = 5, b = 3;
int result = a % b; // result = 2
```

## 自增运算符

```c++
int a = 5;
a++; // 后缀自增, a = 6
++a; // 前缀自增, a = 7
```

## 自减运算符

```c++
int a = 5;
a--; // 后缀自减, a = 4
--a; // 前缀自减, a = 3
```

## 例子

```c++
#include <iostream>

int main() {

    int a1 = 10;
    int b1 = 3;

    std::cout << a1+b1 << std::endl;
    std::cout << a1-b1 << std::endl;
    std::cout << a1*b1 << std::endl;
    std::cout << a1/b1 << std::endl;
    std::cout << a1%b1 << std::endl;
    
    double a2 = 0.5;
    double b2 = 0.22;
    std::cout << a2 / b2 << std::endl;

    return 0;
}
```

```shell
qy@localhost:~/cmakelist_test/bin$ /home/qy/cmakelist_test/bin/cmakelist_test
13
7
30
3
1
2.27273
```

