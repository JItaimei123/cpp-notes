# 1.string的基本操作以及字符串的迭代器遍历操作

```C++
#include<iostream>
#include<string>
using namespace std;
void test_string()
{
    string s1;
    string s2("hello world");
    s2[0] = 'x';
    s1 = s2;

    cout <<s1<<" "<< s2 << " "  <<s1.size()<< endl;
```

```C++
for (size_t i = 0; i < s1.size(); i++)
    {
        cout << s1[i] << " ";
    }
```

```C++
string::iterator it = s2.begin();
    while (it != s2.end())
     //end代表这个迭代器指向最后一个字符之后的位置，不代表任何有效字符，通常用作循环结束标志。
    {
        cout << *it << " ";
        it++;
    }
```

```C++
string::reverse_iterator rit = s2.rbegin();
//rbegin表示指向最后一个元素
    while (rit != s2.rend())
    //rend表示指向第一个元素之前的位置
    {
        cout << *rit << " ";
        rit++;
    }
```

```C++
string::const_iterator cit = s2.begin();
while (cit != s2.end())
    {
        cout << *cit << " ";
        cit++;//cit可以++，const_iterator类似const int*  而不是const * int
    }
```

```C++
string::const_reverse_iterator rcit = s2.rbegin();
//可用auto rcit = s2.rbegin();替代
while (rcit != s2.rend())
    {
        cout << *rcit << " ";
        rcit++;
    }
```

### 以上的迭代器类型均可用auto

掌握了这四种迭代器就相当于掌握了所有容器的访问方式。普通迭代器可读可写。

### 正向 vs 反向遍历总结

|特性|正向遍历|反向遍历|
|-|-|-|
|起始迭代器|`begin()`|`rbegin()`|
|结束迭代器|`end()`|`rend()`|
|遍历顺序|从前往后|从后往前|

# 2.auto和范围for

## auto关键字

```C++
auto x = 42;          // int
auto pi = 3.14;       // double
auto p = &x;          // int*
```

- `auto` 让编译器根据初始化表达式自动推导类型
- 声明指针时 `auto` 与 `auto*` 等价
- 不能用作函数参数类型，不能用于声明数组

```C++
#include <iostream>
#include <string>
#include <map>
using namespace std;
int main()
{
    std::map<std::string, std::string> dict = {
        { "apple", "苹果" },
        { "orange", "橙子" },
        { "pear", "梨" }
    };
    auto it = dict.begin();
    while (it != dict.end()) 
    {
        cout << it->first << ":" << it->second << endl;
        ++it;
    }
    return 0;
}
```

## 范围for

```C++
int array[] = { 1, 2, 3, 4, 5 };
for (auto& e : array) { e *= 2; }
for (auto e : array) { cout << e << " "; }
```

- `auto& e`：引用，修改影响原数组
- `auto e`：值拷贝，修改不影响原数组

# 3.insert用法

```C++
string s = "hello";
s.insert(2, "123");                    // "he123llo"
s.insert(0, "prefix");                 // "prefixhello"
s.insert(0, 3, '*');                   // "***hello"
s.insert(s.begin() + 2, 'X');         // 迭代器版本
```

# 4.erase用法

```C++
string s = "Hello World!";
s.erase(5, 1);       // "HelloWorld!"
s.erase(0, 5);       // "World!"
s.erase(5);          // "World"
```

# 5.find用法

```C++
string s = "hello world, hello c++";
size_t pos = s.find("hello");          // 0
pos = s.find("hello", 1);              // 13
if (s.find("java") == string::npos) { /* 没找到 */ }

// 替换空格
string s2 = "hello world";
size_t p = 0;
while ((p = s2.find(' ', p)) != string::npos) {
    s2.replace(p, 1, "%%");
    p += 2;
}
```

|函数|作用|
|-|-|
|`find()`|正向查找|
|`rfind()`|反向查找|
|`find_first_of()`|查找字符集中任一字符首次出现|
|`find_first_not_of()`|查找第一个不在字符集中的字符|

# 6.operator +=

```C++
string s = "Hello";
s += '!';           // "Hello!"
s += " World";      // "Hello! World"
string t = " Hi";
s += t;             // "Hello! World Hi"
```

# 7.substr

```C++
string s = "Hello, World!";
string s1 = s.substr(0, 5);     // "Hello"
string s2 = s.substr(7);         // "World!"
string filename = "document.txt";
string ext = filename.substr(filename.find('.') + 1); // "txt"
```

# 8.operator+（非成员函数）

```C++
string a = "Hello";
string b = "World";
string c = a + " " + b;   // "Hello World"
```

- 不修改原字符串，返回新字符串
- 至少一个操作数是 `std::string`

# 9.getline

```C++
string line;
getline(cin, line);                  // 读取一行（含空格）
getline(cin, line, ';');             // 自定义分隔符
```

- 读取到换行符停止，丢弃换行符
- 与 `operator>>` 混用时注意换行符残留

# 10.字符串与数字转换

```C++
int a = stoi("123");                  // 123
long b = stol("2147483648");
long long c = stoll("1234567890123456789");
double d = stod("3.14159");

string s1 = to_string(123);           // "123"
string s2 = to_string(3.14159);       // "3.141590"
```