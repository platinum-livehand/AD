## 1. extern的全局变量

```c++
// test.h
#ifndef TEST_H
#define TEST_H
#include <iostream>

using namespace std;

extern int c;

void test_showc();

void test_setc();

#endif // TEST_H
```

```c++
// test.cpp
#include "test.h"

int c = 1;

void test_showc()
{
    cout << "test c = " << c << endl;
}

void test_setc()
{
    c = 2;
}
```

```c++
// test2.h
#ifndef TEST2_H
#define TEST2_H

#include "test.h"

void test2_showc();

void test2_setc();

#endif // TEST2_H
```

```c++
// test2.cpp
#include "test2.h"

void test2_showc()
{
    cout << "c = " << c << endl;
}

void test2_setc()
{
    c = 3;
}
```

```c++
// main.cpp
#include "inc\test2.h"
#include "inc\test.h"

int main()
{
    cout << "====== c initA ======" << endl;
    cout << "main c = " << c << endl;
    test_showc();
    test2_showc();

    cout << "====== test update c ======" << endl;
    test_setc();
    cout << "main c = " << c << endl;
    test_showc();
    test2_showc();

    cout << "====== test2 update c ======" << endl;
    test2_setc();
    cout << "main c = " << c << endl;
    test_showc();
    test2_showc();

    cout << "====== main update c ======" << endl;
    c = 4;
    cout << "main c = " << c << endl;
    test_showc();
    test2_showc();

    cout << "====== all update c ======" << endl;
    c = 5;
    test2_setc();
    test_setc();

    cout << "main c = " << c << endl;
    test_showc();
    test2_showc(); 
    
    return 0;
}
```

```powershell
#运行结果
====== c initA ======
main c = 1
test c = 1
c = 1
====== test update c ======
main c = 2
test c = 2
c = 2
====== test2 update c ======
main c = 3
test c = 3
c = 3
====== main update c ======
main c = 4
test c = 4
c = 4
====== all update c ======
main c = 2
test c = 2
c = 2
```

## 2. static 的全局变量

```c++
// test.h
#ifndef TEST_H
#define TEST_H
#include <iostream>

using namespace std;

static int c = 1;

void test_showc();

void test_setc();

#endif // TEST_H
```

```c++
// test.cpp
#include "test.h"

void test_showc()
{
    cout << "test c = " << c << endl;
}

void test_setc()
{
    c = 2;
}
```

```c++
// main.cpp
#include "inc\test.h"

int main()
{
    cout << "====== c init ======" << endl;
    cout << "main c = " << c << endl;
    test_showc();

    cout << "====== test update c ======" << endl;
    test_setc();
    cout << "main c = " << c << endl;
    test_showc();

    cout << "====== main update c ======" << endl;
    c = 3;
    cout << "main c = " << c << endl;
    test_showc();
    
    return 0;
}
```

```powershell
#运行结果
====== c init ======
main c = 1
test c = 1
====== test update c ======
main c = 1
test c = 2
====== main update c ======
main c = 3
test c = 2
```

