# C++课程第一次作业

## 第二章

### 题2.23
答：能。
1. 我们在使用指针是若已经定义了对象后再定义指向它的指针，或者已经将它初始化为nullptr或0，判断p是否指向合法的对象，就可以把p作为if语句的条件，p的值为nullptr，条件为假，反之，条件为真。
2. 有的程序中指针p并未初始化，可以将if(p)置于try结构中。若程序进入catch语句，表明p没有指向合法的对象，反之，p指向合法的对象。
### 题2.24
答：p合法。void*可以用于存放任意对象的地址。lp非法。lp是长指针类型，而i是int型，二者类型不匹配。

### 题2.25

答：(a)ip 整型指针，指向一个整型变量，值为所指整型数对象在内存中的地址。i是一个整型数，没有初值。r是变量i的引用，r的值是i的值。

(b)i是一个整型数，没有初值。ip是一个整型指针，不指向某一具体变量，值为0。

(c)ip是整型指针，值为所指整型数对象在内存中的地址。ip2是一个整形数，无初值。

### 题2.35

答：i为整型常量，j可以推测为整数，k可以推测为整型常量，p可以推测为指向整型常量的指针，j2可以推测为整型常量，k2可以推测为整型常量的引用。

```c++
// Copyright 2019 Aurora 
#include <iostream>
#include<typeinfo>
int main() {
    const int i = 42;
    auto j = i;
    const auto &k = i;
    auto *p = &i;
    const auto j2 = i, &k2 = i;
    std::cout << typeid(i).name() << std::endl;
    std::cout << typeid(j).name() << std::endl;
    std::cout << typeid(k).name() << std::endl;
    std::cout << typeid(p).name() << std::endl;
    std::cout << typeid(j2).name() << std::endl;
    std::cout << typeid(k2).name() << std::endl;

    return 0;
}

```

## 第三章

### 题3.4

```c++
// Copyright 2019 Aurora
#include <iostream>
#include<string>

int main() {
    std::string s1, s2;
    std::cout << "输入两个字符串：" << std::endl;
    std::cin >> s1 >> s2;
    if (s1 == s2) {
        std::cout << "相等" << std::endl;
    } else if (s1 > s2) {
        std::cout << "大于" << std::endl;
    } else {
        std::cout << "小于" << std::endl;
    }
    return 0;
}

```

```c++
// Copyright 2019 Aurora
#include <iostream>
#include<string>

int main() {
    std::string s1, s2;
    std::cout << "输入两个字符串：" << std::endl;
    std::cin >> s1 >> s2;
    auto len1 = s1.size();
    auto len2 = s2.size();
    if (len1 == len2) {
        std::cout << "相等" << std::endl;
    } else if (len1 > len2) {
        std::cout << s1 << std::endl;
    } else {
        std::cout << s2 << std::endl;
    }
    return 0;
}

```

### 题3.5

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <string>

int main() {
    char flag;
    std::string s, ans;
    std::cout << "请输入第一个字符串：" << std::endl;
    while (std::cin >> s) {
        ans += s;
        std::cout << "是否有新的字符串(y or n): " << std::endl;
        std::cin >> flag;
        if (flag == 'n')
            break;
    }
    std::cout << "连接成的大字符串：" << ans << std::endl;
    return 0;
}

```

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <string>

int main() {
    char flag;
    std::string s, ans;
    std::cout << "请输入第一个字符串：" << std::endl;
    while (std::cin >> s) {
        ans += s;
        std::cout << "是否有新的字符串(y or n): " << std::endl;
        std::cin >> flag;
        if (flag == 'n') {
            break;
        } else {
            ans = ans + ' ';
        }
    }
    std::cout << "连接成的大字符串：" << ans << std::endl;
    return 0;
}

```

### 题3.20

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <vector>

int main() {
    std::vector<int> p;
    int x;
    std::cout << "请输入一组数字：" << std::endl;
    while (std::cin >> x) {
        p.push_back(x);
    }
        if (p.size() == 0) {
            std::cout << "没有元素" << std::endl;
            return -1;
        }
        std::cout << "相邻整数的和为：" << std::endl;
        for (decltype(p.size()) i = 0; i < p.size() - 1; i += 2) {
            std::cout << p[i] + p[i+1] << " ";
        }
    if (p.size() % 2 !=0)
        std::cout << p[p.size()-1] << std::endl;

    return 0;
}

```

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <vector>

int main() {
    std::vector<int> p;
    int x;
    std::cout << "请输入一组数字：" << std::endl;
    while (std::cin >> x) {
        p.push_back(x);
        char c = std::cin.get();
        if (c == '\n')
            break;
    }
        if (p.size() == 0) {
            std::cout << "没有元素" << std::endl;
            return -1;
        }
        std::cout << "首位两项的和依次为：" << std::endl;
        for (decltype(p.size()) i = 0; i < p.size() / 2; i ++) {
            std::cout << p[i] + p[p.size()-i-1] << " ";
        }
    if (p.size() % 2 !=0)
        std::cout << p[p.size() / 2];
    std::cout << std::endl;
    return 0;
}

```

### 题3.23

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <vector>

int main() {
    std::vector<int> p;
    srand(time(0));
    for (int i = 0; i <10; i++)
        p.push_back(rand() % 1000);
    std::cout << "原始数值为：" << std::endl;
    for (auto it = p.cbegin(); it < p.cend(); it++)
        std::cout << *it << " ";
    std::cout << std::endl;
    std::cout << "使用迭代器后的数值：" << std::endl;
    for (auto it = p.cbegin(); it < p.cend(); it++)
        std::cout << *it*2 << " ";
    std::cout << std::endl;
    return 0;
}

```

## 第六章

### 题6.10

```c++
// Copyright 2019 Aurora
#include <iostream>

void swap_it(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

int main() {
    int x = 1, y = 2;
    int *p = &x, *q = &y;
    std::cout << "原始值：" << *p << " " << *q << std::endl;
    swap_it(p, q);
    std::cout << "交换后：" << *p << " " << *q <<std::endl;
    return 0;
}

```

### 题6.19

答：（a）非法。函数声明只包含一个参数，而调用过程给了两个参数，无法通过编译。

（b）合法。调用函数的参数数量相对应，字面值常量可以作为常量引用形参的值，字符可以作为char类型的值。（c）合法。66在函数调用过程中会自动转为double类型。

（d）合法。vec.begin()和vec.end()类型都是形参vector<int>::iterator类型。3.8在函数调用过程中会自动转换为int类型。

### 题6.39

答：（a）非法。第二个函数只接受整型常量为实参，而顶层const不影响传入函数的对象，所以一个拥有顶层const的形参无法与没有顶层const的形参相区分。

（b）非法。c++中重载函数必须在形参的数量或类型上有所不同，如果两个同名函数的形参数量和类型都一样，虽然返回类型不同，也不符合规范。

（c）合法。两函数为重载函数，他们在形参类型和返回值类型上都有所区分。

## 第七章

### 题7.16

答：在类的定义中，可以没有或者包含多个访问说明符，对访问说明符的出现次数和位置都没有严格限定，每个访问说明符指定接下来的成员的访问级别，有效范围直到出现下一个访问说明符或者到达类的结尾为止。

定义在public说明符之后的成员在整个程序内可被访问，public成员定义类的接口，构造函数和一部分成员函数应定义在public说明符之后。

定义在private说明符之后的成员可以被类的成员函数访问，但是不能被使用该类的代码访问，private部分封装了类的实现细节。

### 题7.27

test.h文件

```c++
// Copyright 2019 Aurora
#ifndef TEST_H_
#define TEST_H_
#include <iostream>
#include <string>
#include <utility>
class Screen {
 private:
    unsigned height = 0, width = 0;
    unsigned cursor = 0;
    std::string contents;

 public:
    Screen() = default;
    Screen(unsigned ht, unsigned wd) : height(ht), width(wd),
            contents(ht * wd, ' ') { }
    Screen(unsigned ht, unsigned wd, char c)
        : height(ht), width(wd), contents(ht *wd, c) { }

 public:
    Screen& move(unsigned r, unsigned c) {
        cursor = r * width + c;
        return *this;
    }
    Screen& set(char ch) {
        contents[ cursor ] = ch;
        return *this;
    }
    Screen& set(unsigned r, unsigned c, char ch) {
        contents[ r * width + c ] = ch;
        return *this;
    }
    Screen& display() {
        std::cout << contents;
        return *this;
    }
};
#endif  // TEST_H_

```

.cpp文件

```c++
// Copyright 2019 Aurora
#include <iostream>
#include"test.h"
// using namespace std;
int main() {
    Screen myScreen(5, 5, 'X');
    myScreen.move(4, 0).set('#').display();
    std::cout << "\n";
    myScreen.display();
    std::cout << "\n";
    return 0;
}

```

### 题7.49

答：

（a）编译通过。编译器先用string对象s自动创建一个Sales_data对象，然后这个新生成的临时对象传给combine的类型为Sales_data形参，函数能正确执行并返回结果。

（b）编译错误。combine函数的参数是一个非常量引用，而s是一个string对象，编译器会自动创建一个Sales_data临时对象，但是它无法传给combine所需的非常量引用。

（c）编译错误。combine被声明成了常量成员函数，无法修改成员的值。

### 题7.58

答：

1. rate和vec的初始化错误。类的内部除了静态常量成员外，其他静态成员不能在类的内部初始化。

2. 类外部的两条语句没有给出静态成员的初始值。