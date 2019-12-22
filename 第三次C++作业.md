# C++第三次作业

## 第八章

### 题8.4

```c++
// Copyright 2019 Aurora
#include<iostream>
#include<fstream>
#include<string>
#include<vector>

int main() {
    ifstream in("data");
    if (!in) {
        cerr << "can't open the file" << std::endl;
        return -1;
    }

    std::string line;
    std::vector<string> words;
    while (getline(in, line)) {
        words.push_back(line);
    }
    in.close();

    std::vector<string>::const_iterator it = words.begin();
    while(it != words.end()) {
        std::cout << *it << std::endl;
        it++;
    }

    return 0;

}

```

### 题8.7

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <fstream>
#include "Sales_data.h"

int main(int argc, char *argv[]) {
    if(argc != 3) {
        std::cerr << "please give the file name" << std::endl;
        return -1;
    }
    ifstream in(argv[l]);
    if (!in) {
        std::cerr << "can't open the file" << std::endl;
        return -1;
    }

    ofstream out(argv[2]);
    if (!out) {
        std::cerr << "can't output the file" << std::endl;
        return -1;
    }

    Sales_data total;
    if (read(in, total)) {
        Sales_data trans;
        while(read(in, trans)) {
            if(total.isbn() == trans.isbn())
                total.combine(trans);
            else {
                print(out, total) << std::endl;
                total = trans;
            }
        }
        print(out, total) << std::endl;
    } else {
        std::cerr << "no data" << std::endl;
    }
    return 0;
}

```

### 题8.9

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <sstream>
#include <string>
#include <stdexcept>

istream & f(istream & in) {
    std::string v;
    while (in >> v, !in.eof()) {
        if (in.bad())
            throw runtime_error("IO流错误");
        if (in.fail()) {
            std::cerr << "数据错误，请重试" << std::endl;
            in.clear();
            in.ignore(100, '\n');
            continue;
        }
        std::cout << v << std::endl;
    }
    in.clear();
    return in;
}

int main() {
    ostringstream msg;
    msg << "book" << std::endl;
    istringstream in(msg.str());
    f(in);
    return 0;
}

```

## 第十六章

### 题16.11

不能直接使用`ListItem`，应该使用`ListItem<elemType>`。

### 题16.12

```c++
// Copyright 2019 Aurora
#ifndef BLOBPTR_H
#define BLOBPTR_H

#include "Blob.h"
#include <memory>
#include <vector>

template <typename> class BlobPtr;

template <typename T>
bool operator ==(const BlobPtr<T>& lhs, const BlobPtr<T>& rhs);

template <typename T>
bool operator < (const BlobPtr<T>& lhs, const BlobPtr<T>& rhs);


template<typename T> class BlobPtr
{
    friend bool operator ==<T>
    (const BlobPtr<T>& lhs, const BlobPtr<T>& rhs);

    friend bool operator < <T>
    (const BlobPtr<T>& lhs, const BlobPtr<T>& rhs);

public:
    BlobPtr() : curr(0) { }
    BlobPtr(Blob<T>& a, std::size_t sz = 0) :
        wptr(a.data), curr(sz) { }

    T& operator*() const {
        auto p = check(curr, "dereference past end");
        return (*p)[curr];
    }

    //! prefix
    BlobPtr& operator++();
    BlobPtr& operator--();

    //! postfix
    BlobPtr operator ++(int);
    BlobPtr operator --(int);

private:
    //! returns  a shared_ptr to the vector if the check succeeds
    std::shared_ptr<std::vector<T>>
         check(std::size_t, const std::string&) const;

    std::weak_ptr<std::vector<T>> wptr;
    std::size_t curr;

};

//! prefix ++
template<typename T>
BlobPtr<T>& BlobPtr<T>::operator ++() {
    //! if curr already points past the end of the container, can't increment it
    check(curr,"increment past end of StrBlob");
    ++curr;
    return *this;
}

//! prefix --
template<typename T>
BlobPtr<T>& BlobPtr<T>::operator --() {
    -- curr;
    check(curr,"decrement past begin of BlobPtr");

    return *this;
}


//! postfix ++
template<typename T>
BlobPtr<T> BlobPtr<T>::operator ++(int) {
    BlobPtr ret = *this;
    ++*this;

    return ret;
}

//! postfix --
template<typename T>
BlobPtr<T> BlobPtr<T>::operator --(int) {
    BlobPtr ret = *this;
    --*this;

    return ret;
}

template<typename T> bool operator==(const StrPtr<T> &lhs, const StrPtr<T> &rhs {
    if (_l.ptr.lock() != _r.ptr.lock()) {
		throw runtime_error("ptrs to different Blobs!");
	}
	return _l.i == _r.i;
}

template<typename T> bool operator< (const BlobPtr<T> &lhs, const BlobPtr<T> &rhs) {
	if (_l.ptr.lock() != _r.ptr.lock()) {
		throw runtime_error("ptrs to different Blobs!");
	}
	return _l.i < _r.i;
}

#endif // BLOBPTR_H

```

```c++
// Copyright 2019 Aurora
#ifndef BLOB_H
#define BLOB_H
#include <memory>
#include <vector>

template<typename T> class Blob
{
public:
    typedef T value_type;
    typedef typename std::vector<T>::size_type size_type;

    Blob();
    Blob(std::initializer_list<T> il);

    size_type size() const { return data->size(); }
    bool      empty() const{ return data->empty();}

    void push_back(const T& t) { data->push_back(t); }
    void push_back(T&& t)      { data->push_back(std::move(t));}
    void pop_back();

    T& back();
    T& operator[](size_type i);

    const T& back() const;
    const T& operator [](size_type i) const;

private:
    std::shared_ptr<std::vector<T>> data;

    void check(size_type i, const std::string &msg) const;
};

template<typename T>
Blob<T>::Blob() : data(std::make_shared<std::vector<T>>())
{}

template<typename T>
Blob<T>::Blob(std::initializer_list<T> il):
    data(std::make_shared<std::vector<T>>(il)){ }

template<typename T>
void Blob<T>::check(size_type i, const std::string &msg) const
{
    if(i >= data->size())
        throw std::out_of_range(msg);
}

template<typename T>
T& Blob<T>::back()
{
    check(0,"back on empty Blob");
    return data->back();
}

template<typename T>
const T& Blob<T>::back() const
{
    check(0,"back on empty Blob");
    return data->back();
}


template<typename T>
T& Blob<T>::operator [](size_type i)
{

    check(i,"subscript out of range");
    return (*data)[i];
}


template<typename T>
const T& Blob<T>::operator [](size_type i) const
{

    check(i,"subscript out of range");
    return (*data)[i];
}

template<typename T>
void Blob<T>::pop_back()
{
    check(0,"pop_back on empty Blob");
    data->pop_back();
}

#endif // BLOB_H

```

### 题16.19

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <vector>
#include <list>

template<typename Container>
std::ostream& print(Container& c, std::ostream &os);


int main() {
    std::vector<int> v = {1,23,6,4,5,7,4};
    std::list<std::string> l = {"ss","sszz","saaas","s333s","ss2","sss"};
    print2(v, std::cout);
    print2(l, std::cout);

    return 0;
}


template<typename Container>
std::ostream & print(Container &c, std::ostream &os) {
    typedef typename Container::size_type size_type;
    auto it = c.begin();
    for(size_type i = 0; i!= c.size(); ++i)
        os << *it++ << "\n";
    return os;
}



```

### 题16.41

```c++
// Copyright 2019 Aurora
#ifndef REU_TYPE_H
#define REU_TYPE_H

template <typename T> auto sum(const T&a,const T&b) ->decltype(a+b){
	return a+b;
}
#endif REU_TYPE_H

```

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <vector>
#include <list>
#include <string>
#include "Reu_type.h"

int main(int argc,char** argv)
{

	int a = 566669;
	int b = 595955;
	std::cout<<sum(a,b);
	cin.get();
	return 0;
}

```

### 题16.62

```c++
// Copyright 2019 Aurora
#ifndef SALES_DATA_H
#define SALES_DATA_H

#include <string>
#include <iostream>

class Sales_data {
 friend std::hash<Sales_data>;
 friend std::ostream &operator<<
                         (std::ostream&, const Sales_data&);
 friend std::istream &operator>>(std::istream&, Sales_data&);
 friend bool operator==(const Sales_data &, const Sales_data &);

 friend std::ostream &print(std::ostream&, const Sales_data&);
 friend std::istream &read(std::istream&, Sales_data&);

 public:
	 Sales_data() = default;
	 Sales_data(const std::string &s): bookNo(s) { }
	 Sales_data(const std::string &s, unsigned n, double p):
	           bookNo(s), units_sold(n), revenue(p*n) { }
	 Sales_data(std::istream &);

	 std::string isbn() const { return bookNo; }
	 Sales_data& operator+=(const Sales_data&);
 private:
	 double avg_price() const;  
	 std::string bookNo;
	 unsigned units_sold = 0;
	 double revenue = 0.0;
};


inline
bool compareIsbn(const Sales_data &lhs, const Sales_data &rhs) { return lhs.isbn() < rhs.isbn(); }

inline
bool operator==(const Sales_data &lhs, const Sales_data &rhs) {
	return lhs.isbn() == rhs.isbn() && 
	       lhs.units_sold == rhs.units_sold && 
	       lhs.revenue == rhs.revenue;
}
inline
bool operator!=(const Sales_data &lhs, const Sales_data &rhs) {
	return !(lhs == rhs);
}

Sales_data add(const Sales_data&, const Sales_data&);
std::ostream &print(std::ostream&, const Sales_data&);
std::istream &read(std::istream&, Sales_data&);


Sales_data operator+(const Sales_data&, const Sales_data&);
std::ostream &operator<<(std::ostream&, const Sales_data&);
std::istream &operator>>(std::istream&, Sales_data&);

namespace std {
template<>
struct hash<Sales_data> {
    typedef size_t result_type;
    typedef Sales_data argument_type;
    size_t  operator()(const Sales_data& s) const {
        return hash<string>()(s.bookNo) ^
                hash<unsigned>()(s.units_sold) ^
                hash<double>()(s.revenue);
    }
};
}   

#endif

```

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <memory>
#include <unordered_set>
#include "Sales_data.h"

int main() {
    std::unordered_multiset<Sales_data> mset;
    Sales_data sd("Bible",10,0.98);

    mset.emplace(sd);
    mset.emplace("C++ Primer",5, 9.99);

    for(const auto &item : mset)
        std::cout << "the hash code of " << item.isbn()
                  <<":\n0x" << std::hex << std::hash<Sales_data>()(item)
                  << "\n";

    return 0;
}

```

## 第十二章

### 题12.1

b1包含4个元素

b2包含4个元素

### 题12.10

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <vector>
#include <string>
#include <memory>

void process(std::shared_ptr<int> ptr) {
    std::cout << "inside the process function:" << ptr.use_count() << "\n";
}

int main() {
    std::shared_ptr<int> p(new int(42));
    process(std::shared_ptr<int>(p));

    std::cout << p.use_count() << "\n";
    auto q = p;
    std::cout << p.use_count() << "\n";
    std::cout << "the int p now points to is:" << *p << "\n";
    return 0;
}

```

### 题12.15

```c++
// Copyright 2019 Aurora
#include <iostream>
#include <string>
#include <memory>

struct connection {
    std::string ip;
    int port;
    connection(std::string ip_, int port_):ip(ip_), port(port_){ }
};
struct destination {
    std::string ip;
    int port;
    destination(std::string ip_, int port_):ip(ip_), port(port_){ }
};

connection connect(destination* pDest) {
    std::shared_ptr<connection> pConn(new connection(pDest->ip, pDest->port));
    std::cout << "creating connection(" << pConn.use_count() << ")" << std::endl;
    return *pConn;
}

void disconnect(connection pConn) {
    std::cout << "connection close(" << pConn.ip << ":" << pConn.port << ")" << std::endl;
}

void f(destination &d) {
    connection conn = connect(&d);
    std::shared_ptr<connection> p(&conn, [](connection *p){disconnect(*p);});
    std::cout << "connecting now(" << p.use_count() << ")" << std::endl;
}

int main() {
    destination dest("202.118.176.67", 3316);
    f(dest);
    return 0;
}
```

### 题12.17

（a）不合法。`unique_ptr`需要用一个指针初始化，无法将`int`转化为指针。

（b）合法。可以用`int*`初始化`IntP`

（c）合法。用一个动态指针分配的对象的指针来初始化`IntP`合理

（d）合法

（e）合法

（f）合法

### 题12.18

`unique_ptr`有对象的所有权，不能拷贝和赋值，`release`可以将对象的所有权转移给另一个`unique_ptr`的。

多个`shared_ptr`可以共享对象的所有权。需要共享时可以简单拷贝赋值。

### 题12.19

```c++
// Copyright 2019 Aurora
#ifndef CP5_ex12_19_h
#define CP5_ex12_19_h

#include <vector>
#include <string>
#include <initializer_list>
#include <memory>
#include <exception>

using std::vector; using std::string;

class StrBlobPtr;

class StrBlob {
public:
    using size_type = vector<string>::size_type;
    friend class StrBlobPtr;

    StrBlobPtr begin();
    StrBlobPtr end();

    StrBlob():data(std::make_shared<vector<string>>()) { }
    StrBlob(std::initializer_list<string> 					   il):data(std::make_shared<vector<string>>(il)) { }

    size_type size() const { return data->size(); }
    bool empty() const { return data->empty(); }

    void push_back(const string &t) { data->push_back(t); }
    void pop_back() {
        check(0, "pop_back on empty StrBlob");
        data->pop_back();
    }

    std::string& front() {
        check(0, "front on empty StrBlob");
        return data->front();
    }

    std::string& back() {
        check(0, "back on empty StrBlob");
        return data->back();
    }

    const std::string& front() const {
        check(0, "front on empty StrBlob");
        return data->front();
    }
    const std::string& back() const {
        check(0, "back on empty StrBlob");
        return data->back();
    }

private:
    void check(size_type i, const string &msg) const {
        if (i >= data->size()) throw std::out_of_range(msg);
    }

private:
    std::shared_ptr<vector<string>> data;
};

class StrBlobPtr {
public:
    StrBlobPtr():curr(0) { }
    StrBlobPtr(StrBlob &a, size_t sz = 0):wptr(a.data), curr(sz) { }
    bool operator!=(const StrBlobPtr& p) {return p.curr != curr; }
    string& deref() const {
        auto p = check(curr, "dereference past end");
        return (*p)[curr];
    }
    StrBlobPtr& incr() {
        check(curr, "increment past end of StrBlobPtr");
        ++curr;
        return *this;
    }

private:
    std::shared_ptr<vector<string>> check(size_t i, const string &msg) const {
        auto ret = wptr.lock();
        if (!ret) throw std::runtime_error("unbound StrBlobPtr");
        if (i >= ret->size()) throw std::out_of_range(msg);
        return ret;
    }
    std::weak_ptr<vector<string>> wptr;
    size_t curr;
};

#endif

```



```c++
// Copyright 2019 Aurora
#include "ex12_19.h"

StrBlobPtr StrBlob::begin() {
    return StrBlobPtr(*this);
}
StrBlobPtr StrBlob::end() {
    return StrBlobPtr(*this, data->size());
}

```

### 题12.30

```c++
// Copyright 2019 Aurora
#ifndef Cccc
#define Cccc

#include <string>
#include <fstream>
#include <map>
#include <set>
#include <vector>
#include <iostream>
#include <sstream>

using namespace std;

class QueryResult;
class TextQuery {
public:
	using LineNo = vector<string>::size_type;
	TextQuery(std::ifstream&);
	QueryResult query(const string&) const;
private:
	shared_ptr<vector<string>> input;
	std::map<string, shared_ptr<std::set<LineNo>>> result;
};
class QueryResult {
public:
	friend std::ostream& print(std::ostream&, const QueryResult&);
public:
	QueryResult(const string& s, shared_ptr<std::set<TextQuery::LineNo>> set,
		shared_ptr<vector<string>> v)
		: word(s), nos(set), input(v)
	{
	}
private:
	string word;
	shared_ptr<std::set<TextQuery::LineNo>> nos;
	shared_ptr<vector<string>> input;
};
std::ostream& print(std::ostream&, const QueryResult&);
TextQuery::TextQuery(std::ifstream& ifs) : input(new vector<string>)
{
	LineNo lineNo{0};
	for (string line; std::getline(ifs, line); ++lineNo) {
		input->push_back(line);
		std::istringstream line_stream(line);
		for (string text, word; line_stream >> text; word.clear()) {
			std::remove_copy_if(text.begin(), text.end(),
				std::back_inserter(word), ispunct);
			auto& nos = result[word];
			if (!nos) nos.reset(new std::set<LineNo>);
			nos->insert(lineNo);
		}
	}
}
QueryResult TextQuery::query(const string& str) const

{
	static shared_ptr<std::set<LineNo>> nodate(new std::set<LineNo>);
	auto found = result.find(str);
	if (found == result.end())
		return QueryResult(str, nodate, input);
	else
		return QueryResult(str, found->second, input);
}
std::ostream& print(std::ostream& out, const QueryResult& qr) {
	out << qr.word << " occurs " << qr.nos->size()
		<< (qr.nos->size() > 1 ? " times" : " time") << std::endl;
	for (auto i : *qr.nos)
		out << "\t(line " << i + 1 << ") " << qr.input->at(i) << std::endl;
	return out;
}

#endif

```

```C++
// Copyright 2019 Aurora
#include "Chapter12.h"
#include <iostream>
using namespace std;
void runQueries(std::ifstream& infile) {
	TextQuery tq(infile);
	while (true) {
	cout << "enter word to look for, or q to quit: ";
	string s;
	if (!(cin >> s) || s == "q") break;
	print(cout, tq.query(s)) << endl;
	}
}

int main()
{
	ifstream file("1.txt");
	runQueries(file);
}

```

