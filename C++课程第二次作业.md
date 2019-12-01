# c++第二次作业 

## 第九章

### 题9.11

答：

#### 1、`vector<int> v` ,容器中没有元素

#### 2、`vector<int> v(10)` ,容器中有10个`int` 大小的空间,值为零

#### 3、`vector<int> v(5, 2)`,容器中有5个值为2的`int` 类型整数

#### 4、`vector<int> v{1, 2, 3, 4, 5}` ,容器中有1，2， 3， 4， 5五个整数

#### 5、`vector<int> v(vec)` 括号中接受另一个vector容器，可以将`vec` 容器中的值拷贝到`v` 容器中

#### 6、`vector<int> v(vec.begin(), vec.begin() + 2)` ，括号中接受两个同一个容器的两个迭代器，将这两个迭代器范围的元素拷贝到`v` 容器中（区间为左闭右开)





### 题９.20

#### 问题

编写程序，从一个list<int>拷贝元素到两个deque中。值为偶数的所有元素都拷到一个deque中，而奇数值元素都拷到另一个deque中。

#### 解答

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <list>
#include <deque>

int main()
{
    std::list<int> mylist = {1, 2, 3, 4, 5, 6, 7, 8};    // init list
    std::deque<int>  odd, even;

    for (auto iter = mylist.cbegin(); iter != mylist.cend(); iter++) {
        if (*iter & 1 )
            odd.push_back(*iter);
        else
            even.push_back(*iter);
        }
        std::cout << "奇数值有：";
        for (auto iter = odd.cbegin(); iter != odd.cend(); iter++)
            std::cout << *iter << " ";
        std::cout << std::endl;

        std::cout <<"偶数值有：";
        for (auto iter = even.cbegin(); iter != even.cend(); iter++)
            std::cout << *iter << " ";
        return 0;
}

```



### 题9.25

#### 1、如果`elem1` 和`elem2` 相等，不会发生删除操作

#### 2、如果`elem2` 是尾后迭代器，删除从第一个迭代器参数开始到容器最后一个元素 

#### 3、如果`elem1` 和 `elem2` 都是尾后迭代器，不会发生删除操作

###  

### 题9.29

#### 问题

假定vec包含25个元素，那么vec.resice(100)会做什么？如果接下来调用resize(10)会做什么？

#### 解答

vec中本来包含25个数，调用vec.resize(100)会向vec末尾加入75个初始化为０的元素，而用vec.resize(10)会将vec后部分的90个元素删除。



### 题9.43

#### 问题

编写函数，接受三个string参数s、oldVal和newVal。使用迭代器及insert和erase函数将s中所有oldVal替代为nemVal。测试你的程序，用它替换通用的简写形式，如，将“tho”替换为“thoutgh”,将“thru”替换为“through”。

#### 解答

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <vector>
#include <string>


void replace_string(std::string &s,
                                        const std::string &oldVal,
                                        const std:: string &newVal) {
    auto len = oldVal.size();
    if (!len)
        return;
    auto iter = s.begin();
    while (iter != s.end()) {
        auto iter1 = iter
        auto iter2 = oldVal.begin();
        while (iter2 != oldVal.end() && *iter1 == *iter2) {
            iter1++;
            iter2++;
    }
        if (iter2 == oldVal.end()) {
            iter = s.erase(iter, iter1);
            if (newVal.size()) {
                iter2 = newVal.end();
                do {
                    iter2--;
                    iter = s.insert(iter, *iter2);
                }while (iter2 > newVal.begin());
            }
            iter += newVal.size();
        } else {
            iter++;
        }
    }
}

int main() {
    std::string s = "the tho thru";
    replace_string(s, "tho", "though");
    std::cout << s << std::endl;
    replace_string(s, "thru", "through");
    std::cout << s << std::endl;
}

```

### 题9.52

#### 题目

使用stack处理括号化的表达式。当你看到一个左括号，将其记录下来。当你在一个左括号之后看到一个右括号，从stack中pop对象，直至遇到左括号，将左括号也一起弹出栈。然后将一个值（括号内的运算结果）push到栈中，表示一个括号化的子表达式已经处理完毕，被其运算结果所替代。

#### 解答

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<string>
#include<deque>
#include<stack>
#include<stdexcept>
enum obj_type {
	LP, RP, ADD, SUB, VAL
};
struct obj {
	obj_type t;
	double v;
	obj(obj_type type, double val = 0) {
		t = type;
		v = val;
	}
};

inline void skip(string &exp, size_t &p) {
	p = exp.find_first_not_of(" ", p);
}
inline void new_val(stack<obj> &so, double v) {
	if (so.empty() || so.top().t == LP) { 
		so.push(obj(VAL, v));
	}
	else if (so.top().t == ADD || so.top().t == SUB) {
		obj_type type = so.top().t;
		so.pop();
		if (type == ADD) {
			v += so.top().v;
		}
		else {
			v = so.top().v - v;
		}
		so.pop();
		so.push(obj(VAL, v));
	}
	else throw invalid_argument("缺少运算符");
}

int main() {
	std::stack<obj> so;
	std::string exp;
	size_t p = 0, q;
	double v;
	std::cout << "请输入表达式 ";
	getline(cin, exp);
	while (p < exp.size()) {
		skip(exp, p);
		if (exp[p] == '(') {
			so.push(obj(LP));
			p++;
			std::cout << "push LP" << std::endl;
		}
		else if (exp[p] == '+' || exp[p] == '-') {
			if (so.empty() || so.top().t != VAL)
				throw invalid_argument("缺少运算数");
			if (exp[p] == '+') 
				so.push(obj(ADD));
			else so.push(obj(SUB));
			p++;
		}
		else if (exp[p] == ')') { 
			p++;
			if (so.empty()) 
				throw invalid_argument("未匹配的右括号");
			if (so.top().t == LP) 
				throw invalid_argument("空括号");
			if (so.top().t == VAL) { 
				v = so.top().v;
				so.pop();
				if (so.empty() || so.top().t != LP) {
					throw invalid_argument("未匹配右括号");
				}
				so.pop();
				new_val(so, v); 
			}
			else throw invalid_argument("缺少运算数"); 
		}
		else { 
			v = stod(exp.substr(p), &q);
			p += q;
			new_val(so, v);
		}
	}
	if (so.size() != 1 || so.top().t != VAL)
		throw invalid_argument("非法表达式");
	std::cout << so.top().v << std::endl;
	return 0;
}

```





## 第十章

#### 题10.3

用accumulate求一个vector<int>中的元素之和

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<fstream>
#include<vector>
#include<algorithm>
#include<numeric>
int main(int argc, char *argv[]) {
	if stream in(argv[1]);
	if (!in) {
		std::cout << "打开文件失败" << std::endl;
		exit(1);
	}
	std::vector<int> v;
	int val;
	while (in >> val) {
		v.push_back(val);
	}
	std::cout << accumulate(v.begin(), v.end(), 0) <<std::endl;
	return 0;
}

```





### 题10.15

编写一个lambda，捕获它所在函数的int，并接受一个int参数。lambda应该返回捕获的int和int参数的和

```c++
// Copyright 2019 Aurora
#include<iostream>
void add(int a) {
	auto sum = [a](int b) { return a + b; };
	std::cout << sum(1) << std::endl;
}
int main(void) {
	add(1);
	add(2);
	return 0;
}

```



### 题10.34

使用反向迭代器逆序打印一个vector

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<fstream>
#include<vector>
#include<algorithm>
int main(int argc, char *argv[]) {
	if (argc != 2) {
		std::cout << "用法: execise.exe in_file" << std::endl;
		return -1;
	}
	ifstream in(argv[1]);
	if (!in) {
		std::cout << "打开输入文件失败" << std::endl;
		exit(1);
	}
	std::vector<int> v;
	int vi;
	while (in >> vi) {
		v.push_back(vi);
	}
	for (auto r_iter = v.crbegin(); r_iter != v.rend(); ++r_iter) {
		std::cout << *r_iter << " ";
	}
	std::cout << std::endl;
	return 0;
}

```



### 题10.42

使用list代替vector重新实现去除重复单词的程序

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<fstream>
#include<list>
#include<string>
#include<algorithm>
inline void output_words(list<string> &words) {
	for (auto iter = words.begin(); iter != words.end(); ++iter) {
		std::cout << *iter << std::endl;
	}
	std::cout << std::endl;
}
void elimDups(std::list<string> &words) {
	output_words(words);
	words.sort();
	output_words(words);
	words.unique();
	output_words(words);
}
int main(int argc, char *argv[]) {
	if stream in(argv[1]);
	if (!in) {
		cout << "打开文件失败" << endl;
		exit(1);
	}
	std::list<string> words;
	std::string word;
	while (in >> word) {
		words.push_back(word);
	}
	elimDups(words);
	return 0;
}

```





## 第十一章

### 题11.12

编写程序，读入string和int序列，将每个string和int存入一个pair中，再将pair保存在一个vector中

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<fstream>
#include<utility>
#include<vector>
#include<string>
#include<algorithm>
int main(int argc, char *argv[]) {
	if stream in(argv[1]);
	if (!in) {
		std::cout << "fail" << std::endl;
		exit(1);
	}
	std::vector<std::pair<std::string, int>> data;
	std::string s;
	int v;
	while (in >> s && in >> v) {
		data.push_back(pair<string, int>(s, v));
	}
	for (const auto &d : data) {
		std::cout << d.first << " " << d.second << std::endl;
	}
	return 0;
}

```





### 题11.17

#### set的迭代器是const的，因此只允许访问set中元素，而不能改变set。

#### set的关键字也是const，因此不能通过迭代器来改变set中元素的值

#### 因此，前两个调用试图将vector中的元素复制到set中，是非法的。

#### 而后两个调用将set中的元素复制到vector中，是合法的。



### 题11.38

用unordered_map重写单词技术程和单词转换程序

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<vector>
#include<unordered_map>
#include<string>
#include<stdexcept>
#include<sstream>
#include<fstream>
unordered_map<std::string, std::string> buildMap(ifstream &map_file) {
	unordered_map<std::string, std::string> trans_map;
	std::string key;
	std::string value;
	while (map_file >> key && getline(map_file, value)) {
		if (value.size() > 1) {
			trans_map[key] = value.substr(1);
		}
		else throw runtime_error("no rule for " + key);
	}
	return trans_map;
}
const string & transform(const string &s, const unordered_map<string, string> &m) {
	auto map_it = m.find(s);
	if (map_it != m.cend())
		return map_it->second;
	else return s;
}
void word_transform(ifstream &map_file, ifstream &input) {
	auto trans_map = buildMap(map_file);
	std::string text;
	while (getline(input, text)) {
		istringstream stream(text);
		std::string word;
		bool firstword = true;
		while (stream >> word) {
			if (firstword)
				firstword = false;
			else std::cout << " ";
			std::cout << transform(word, trans_map);
		}
		std::cout << std::endl;
	}
}
int main(int argc, char **argv) {
	if (argc != 3)
		throw runtime_error("wrong number of arguments");
	if stream map_file(argv[1]);
	if (!map_file)
		throw runtime_error("no transformation file");
	if stream input(argv[2]);
	if (!input) 
		throw runtime_error("no input file");
	word_transform(map_file, input);
	return 0;
}

```



## 第十三章

### 题13.12

#### （1）函数结束时，局部变量 `item1` 的生命期结束，被销毁，`Sales_data` 的析构函数被调用

#### （2）`item2`在函数结束时被销毁，`Sales_data` 的析构函数被调用。

#### （3）函数结束时，参数 `accum` 的生命周期结束，被销毁，`Sales_data` 的析构函数被调用。

### 题13.18

定义一个Employee类，它包含雇员的姓名和唯一的雇员证号。为这个类定义默认构造函数，以及接受一个表示雇员姓名的srting的构造函数。每个构造函数应该通过递增一个static数据成员来生成一个唯一的证号

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<string>
class Employee {
private:
	static int sn;
public:
	Employee() { mysn = sn++; }
	Employee(const std::string &s) { name = s; mysn = sn++; }
	//Employee(Employee &e) { name = e.name; mysn = sn++; }
	//Employee& operator=(Employee &rhs) { name = rhs.name; return *this; }
	const std::string &get_name() { return name; }
	int get_mysn() { return mysn; }
private:
	std::string name;
	int mysn;
};
int Employee::sn = 0;
void f(Employee &s) {
	std::cout << s.get_name() << ":" << s.get_mysn() << std::endl;
}
int main(void) {
	Employee a("李"), b = a, c;
	c = b;
	f(a);
	f(b);
	f(c);
	return 0;
}

```



### 题13.46

#### （1）`r1` 必须是右值引用，因为 `f` 返回非引用类型函数，返回值是一个右值

#### （2）`r2` 必须是左值引用，因为下标运算返回的是左值。

#### （3）`r3` 只能是左值引用，因为 `r1` 是一个变量，而变量是左值

#### （4）`r4` 只能是右值引用，因为 `vi[0] * f()` 是一个算术运算表达式，返回右值。



### 题13.49

为你的StrVec, String和Message类添加一个移动构造函数和一个移动赋值运算符

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<string>
String &string::operator=(String &&rhs) noexcept {
	if (this != &rhs) {
		if (p)
			a.deallocate(p, sz);
		p = rhs.p;
		sz = rhs.sz;
		rhs.p = 0;
		rhs.sz = 0;
	}
	return *this;
}
int main(void) {

}

```





### 题13.58

编写新版本的Foo类，其sorted函数中有打印语句，测试这个类。

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<vector>
#include<algorithm>
class Foo {
public:
	Foo sorted() && ;
	Foo sorted() const &;
private:
	std::vector<int> data;
};
Foo Foo::sorted() && {
	std::cout << "右值引用版本" << std::endl;
	sort(data.begin(), data.end());
	return *this;
}
Foo Foo::sorted() const & {
	std::cout << "左值引用版本" << std::endl;
	Foo ret(*this);
	return ret.sorted();
}
int main(void) {
	Foo f;
	f.sorted();
	return 0;
}

```





## 第十四章

### 题14.3

#### （1）`"cobble" == "store"` 应用了C++语言内置版本的 `==` 来比较两个指针。

#### （2）`svec1[0] == svec2[0]` 应用了 `string` 版本的重载 `==` 

#### （3）`svec1 == svec2` 应用了 `vector` 版本的重载 `==`

#### （4）`svec1[0] == "stone"` 应用了 `string` 版本的重载 `==` ，字符串字面常量被转换为 `string`类型

### 

### 题14.20

为Sales_data类定义加法和复合赋值运算符

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<algorithm>
class Sales_data {
	friend Sales_data operator+ (const Sales_data &lhs, const Sales_data &rhs);
public:
	Sales_data& operator+= (const Sales_data &rhs);
};
Sales_data operator+ (const Sales_data &lhs, const Sales_data &rhs) {
	Sales_data sum = lhs;
	sum += rhs;
	return sum;
}
Sales_data& Sales_data::operator+= (const Sales_data &rhs) {
	uints_sold += rhs.units_sold;
	revenue += rhs.revenue;
	return *this;
}
int main(void) {

}

```





### 题14.38

编写一个类令其检查某个给定的string对象的长度是否与一个阈值相等。使用该对象编写程序，统计并报告在输入的文件中长度为1的单词有多少个，长度为2的单词有多少个，长度为10的单词又有多少个

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<vector>
#include<string>
#include<algorithm>
class StrLenIs {
public:
	StrLenIs(int len) :len(len) {}
	bool operator() (const string &str) {
		return str.length() == len;
	}
private:
	int len;
};
void readStr(istream &is, vector<string> &vec) {
	std::string word;
	while (is >> word) {
		vec.push_back(word);
	}
}
int main(void) {
	std::vector<string> vec;
	readStr(cin, vec);
	const int minLen = 1;
	const int maxLen = 10;
	for (int i = minLen; i <= maxLen; ++i) {
		StrLenIs slenIs(i);
		std::cout << "长度 :" << i << ", 数量: " << count_if(vec.begin(), vec.end(), slenIs);
	}
	return 0;
}

```





### 题14.52

#### 对于`ld = si + ld` ， 由于`LongDouble` 不能转换为`SmallInt` ，因此`SmallInt` 的成员`operator+`  和`friend operator+`  都不可行。

#### 由于`SmallInt` 不能转换为，`LongDouble` ，`LongDouble` 的成员`operator+` 和非成员`operator+` 也都不可行。

#### 由于`SmallInt` 可以转换为`int`， `LongDouble`可以转换为`float`和`double`，所以内置的`operator+（int， float）`和`operator+（int， double）`都可行，会产生二义性

#### 对于`ld = ld + si`， 类似加上一个加法表达式，由于`SmallInt`不能转换为`double`，`LongDouble`也不能转换为`SmallInt`，因此`SmallInt`的成员`operator+`和两个非成员`operator+`都不匹配

#### `LongDouble`的`operator+`可行，且为精确匹配。

#### `SmallInt`可以转换为`int`，`LongDouble`可以转换为`float`和`double`，因此内置的`operator+（float， int）`都可行。但是它们都需要类型转换，因此`LongDouble`的成员`operator+`优先匹配

## 

## 第十五章

### 题15.12

#### 有

#### `override`：该关键字可以来说明派生类中的虚函数，更明确的告诉编译器我们想要覆盖掉已存在的虚函数。如果定义了一个函数与基类中的名字相同但是形参列表不同，在不是用`override`关键字的时候这种函数定义是合法的，在使用了`override`关键字之后这种行为是非法的，编译会提示出错。

#### `final`：我们将某个函数定义成`final`后，不允许后续的派生类来覆盖这个函数否则会报错。



### 题15.16

继承函数

```c++
// Copyright 2019 Aurora
class Limited_quote: public Disc_quote {
public:
	Limited_quote(const std::string &book = "", double sales_price = 0.0, size_t qty = 0, double disc_rate = 0.0) :
		Disc_quote(book, sales_price, qty, disc_rate) { }
	double nei_price(size_t cnt) const override {
		if (cnt <= quantity)
			return cnt * (1 - discount) * price;
		else return quantity * (1 - discount) * price + (cnt - quantity) * price;
	}
};
int main(void) {

}

```



### 题15.30

编写Basket类

```c++
// Copyright 2019 Aurora
class Basket {
public:
	void add_item(const std::shared_ptr<Quote> &sale) {
		items.insert(sale);
	}
	double total_receipt(ostream&) const;
private:
	static bool compare(const share_ptr<Quote> &lhs, const share_ptr<Quote> &rhs) {
		return lhs->isbn() < rhs->isbn();
	}
	multiset<shared_ptr<Quote>, decltype(compare)* > item{ compare };
};
double Basket::total_receipt(ostream &os) const {
	double sum = 0.0;
	for (auto iter = items.cbegin(); iter != items.cend(); iter = items.upper_bound(*iter)) {
		sum += price_total(os, **iter, items.count(*iter));
	}
	std::os << "Total Sale:" << sum << std::endl;
	return sum;
}
int main(void) {

}

```

