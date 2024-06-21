# 1. 左值和右值

左值可以取地址，位于等号左边。

右值无法取地址，位于等号右边。

```c++
int a = 10;
```

a 可以通过 `&a` 取地址，位于等号左边，所以 a 是左值。

10 位于等号右边且无法通过 `&10` 取地址，所以10是右值。

```c++
struct A
{
	A(int a=0)
	{
		a_=a;
	}
	int a_;
};

A a=A();
```

a 可以通过 `&a` 取地址，位于等号左边，所以 a 是左值。

`A()` 是个临时值，没办法通过 &A() 取地址，位于等号右边，所以 `A()` 是个右值。

# 2. 左值引用

能指向左值，不能指向右值的就是左值引用。

```c++
#include <iostream>

int main(int argc, char **argv)
{
	int a = 10;
	int &ref_a = a;
	int &ref_b = 10; // 左值引用指向了右值，会编译失败
	return 0;
}
```

引用是变量的别名，由于**右值没有地址，没法被修改**，所以左值引用无法指向右值。

**const 左值引用可以指向右值。**

```c++
const int &ref_b = 10; // 编译通过
```

由于 const 左值引用不会修改指向对象。因此可以指向右值，这也是为什么要使用 cosnt 引用作为函数参数的原因之一。

```c++
void push_back (const value_type val);

vec.push_back(5); // 编译无法通过
```

# 3. 右值引用

**右值引用专门引用右值，不能指向左值，可以修改右值。**

```c++
#include <iostream>

int main(int argc, char **argv)
{
	int &&ref_a_right = 10; //编译通过
	
	int a = 5;

	int &&ref_a_left = a; // 编译不过，右值引用不可以指向左值
	
	ref_a_right = 16; // 右值引用的用途：可以修改右值

	return 0;
}

```

# 4. 左右值引用的本质

**被声明出来的左、右值引用都是左值。**

## 4.1. 右值引用引用左值的办法

```c++
#include <iostream>
using namespace std;
int main(int argc, char **argv)
{	
	int a = 5; // a是左值

	int &ref_a_left = a; // 左值引用
	
	int &&ref_a_right = std::move(a); // 右值引用指向左值，编译通过

	cout << "a=" << a << endl;

	cout << "ref_a_left=" << ref_a_left << endl;
    
	cout << "ref_a_right=" << ref_a_right << endl;

	return 0;
}
```

