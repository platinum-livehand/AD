## auto

​	`auto` 用于 `copy` 一份集合中的数据，对于这种情况，在循环体中无论怎么修改 `copy` 的数据，是不会影响原有集合中的数据的。

```c++
int arr[5] = {0, 1, 2, 3, 4};
cout << "使用auto" << endl;
for (auto a: arr) {
    a += 1;
    cout << a << "\t";
}
cout << endl;

cout << "原数组" << endl;
for (int i = 0; i < 5; i++) {
    cout << arr[i] << "\t";
}
cout << endl;
```

```
使用auto
1       2       3       4       5
原数组
0       1       2       3       4
```

## auto&

​	`auto&` 相当于获取一份集合中数据的引用，那么对于数据的修改，就会直接影响到集合数据本身。

```c++
int arr[5] = {0, 1, 2, 3, 4};
cout << "使用auto &" << endl;
for (auto &a: arr) {
    a += 1;
    cout << a << "\t";
}
cout << endl;

cout << "原数组" << endl;
for (int i = 0; i < 5; i++) {
    cout << arr[i] << "\t";
}
cout << endl;
```

```
使用auto &
1       2       3       4       5
原数组
1       2       3       4       5
```

## const auto&

​	在 `auto&` 的前面加上了 `const` 修饰，意味着它会获取一份集合中数据的引用，但是只可被读取，不能被修改。

```c++
int arr[5] = {0, 1, 2, 3, 4};
cout << "使用const auto &" << endl;
for (const auto &a: arr) {
    a += 1; // // error 不能修改，报错
    cout << a << "\t";
}
```

​	常数据引用，可以接收常数据，非常数据，临时右值，同时也不会调用拷贝构造，避免拷贝构造开销。但是由于返回是 `const` 类型，**常对象以后将无法调用任何非 `const` 的函数。**

## auto&&

​	为了解决上述问题所以，C++11 提出了 `auto&&`用于绑定任何值。

```c++
#include <iostream>
#include <array>
using namespace std;

array<int, 10> aa{11, 22, 33, 44, 55, 66, 77, 88, 99, 10};
void test1()
{
   
    int total = 0;
    for (auto&& value : aa)//也可以 for (auto value : aa)  或者for (const auto& value : aa)
        total += value;  
    //每次都都将容器内遍历后的元素返回给total

    cout << total<<endl;
};

int main()
{
    test1();
    
     for (auto i : aa)
    {
        cout<<i<<"  ";
    }
    cout << endl;

}
```

