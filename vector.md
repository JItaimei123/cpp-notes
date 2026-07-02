## 1. 头文件

```C++
#include <vector>
using namespace std;
```

## 2. 创建和初始化

```C++
vector<int> v1;                          // 空 vector
vector<int> v2(5);                       // [0,0,0,0,0]
vector<int> v3(5, 10);                   // [10,10,10,10,10]
vector<int> v4 = {1,2,3,4,5};            // 列表初始化
vector<int> v5(++v4.begin(),--v4.end()); // {2,3,4}
vector<int> v6(v5);                      // 拷贝构造
```

## 3. 添加元素

```C++
vector<int> v;
v.push_back(10);
v.push_back(20);
v.emplace_back(30);  // C++11，直接构造
```

## 4. 访问元素

```C++
int a = v[0];                     // 下标，不检查越界
v[1] = 25;
int b = v.at(10);                 // at() 检查越界，越界抛异常
int first = v.front();            // 第一个元素
int last = v.back();              // 最后一个元素
```

## 5. 遍历

```C++
for (size_t i = 0; i < v.size(); ++i) cout << v[i] << " ";  // 下标
for (auto e : v) cout << e << " ";                           // 范围 for
for (auto it = v.begin(); it != v.end(); ++it) cout << *it << " "; // 迭代器
```

## 6. 删除元素

```C++
v.pop_back();                               // 删除最后一个
v.erase(v.begin());                         // 删除第一个，O(n)
v.erase(v.begin() + 1, v.begin() + 3);      // 删除区间
v.clear();                                  // 清空
```

## 7. 大小与容量

```C++
v.size();      // 实际元素个数
v.capacity();  // 当前容量
v.empty();     // 是否为空
v.reserve(100); // 预先分配容量
v.resize(20);   // 改变 size
v.resize(15, 5); // 多余的用 5 填充
```

## 8. 其它常用操作

```C++
v1.swap(v2);                         // 交换，O(1)
v1 = v2;                             // 拷贝赋值
v1.insert(v1.begin(), 99);           // 头部插入
int* ptr = v1.data();                // 获取底层数组指针
```

## 9. 与算法配合

```C++
#include <algorithm>
sort(v.begin(), v.end());
sort(v.begin(), v.end(), greater<int>());
reverse(v.begin(), v.end());
```

## 10. 完整示例

```C++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
int main() {
    vector<int> v = {5, 2, 9, 1, 7};
    v.push_back(3);
    sort(v.begin(), v.end());
    for (int x : v) cout << x << " ";
    v.pop_back();
    v.erase(v.begin());
    cout << "size = " << v.size() << endl;
    return 0;
}
```

## 11.vector<T> 中的 T 可以是几乎所有完整对象类型

内置类型、指针、自定义类、其他容器均可。不能放引用和 void。

```C++
vector<string> v1;
v1.push_back("yyyy");  // 隐式转换 string("yyyy")
vector<vector<int>> vv(10, vector<int>(5, 1)); // 10x5 二维数组
v[2][1] = 2;  // 两个 operator[] 调用
```

## 12.范围 for 与 const auto&

```C++
for (const auto& e : v1) { cout << e << " "; }
```

- 避免拷贝（效率）
- 只读访问（安全）

|写法|拷贝？|能修改？|适用场景|
|-|-|-|-|
|`auto e`|✅|❌|需要副本|
|`auto& e`|❌|✅|需要修改|
|`const auto& e`|❌|❌|**只读（推荐）**|

## 13.用 auto& 修改元素

```C++
vector<string> v = {"hello", "world", "c++"};
for (auto& s : v) {
    for (char& c : s) c = toupper(c);
}
// HELLO WORLD C++

for (auto& s : v) { s += " fruit"; }
// "apple fruit", "banana fruit", "cherry fruit"
```