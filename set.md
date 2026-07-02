# 1.set的特性

`set` 是 C++ 中的**关联式容器**，它会自动对元素进行**排序**和**去重**。它的底层实现是**红黑树**，因此插入、删除、查找的时间复杂度都是 **O(log n)**。

## 排序规则

- **默认升序**：`set` 默认使用 `std::less<T>`，也就是按 **`<`** 运算符从小到大排列。
- **自定义排序**：也可以通过第二个模板参数指定排序规则，比如降序：

```C++
set<int> s1;                    // 升序：1, 2, 3, 4, 5
set<int, greater<int>> s2;      // 降序：5, 4, 3, 2, 1
```

## 去重规则

- **不允许重复元素**：插入相同值时，`insert` 会失败。
- 判断重复的依据：**`! (a < b) && ! (b < a)`**，即 a 和 b 在排序规则下相等。

# 2.遍历

```C++
#include <iostream>
#include <set>
using namespace std;

int main() {
    set<int> s;
    s.insert(5); s.insert(3); s.insert(7); s.insert(3);

    // 1. 范围 for（最推荐）
    for (int x : s) { cout << x << " "; }  // 3 5 7
    cout << endl;

    // 2. 迭代器
    for (set<int>::iterator it = s.begin(); it != s.end(); ++it) { cout << *it << " "; }
    cout << endl;

    // 3. auto 迭代器
    for (auto it = s.begin(); it != s.end(); ++it) { cout << *it << " "; }
    cout << endl;

    // 4. 反向遍历
    for (auto it = s.rbegin(); it != s.rend(); ++it) { cout << *it << " "; }  // 7 5 3
    cout << endl;
    return 0;
}
```

# 3.插入(insert)

```C++
#include <iostream>
#include <set>
using namespace std;

int main() {
    set<int> s;
    set<int,greater<int>> s2;
    
    s.insert(5); s.insert(3); s.insert(7);
    auto ret = s.insert(3);
    if (!ret.second) {
        cout << "元素 3 已存在，插入失败" << endl;
    }
    
    for (int x : s) { cout << x << " "; }  // 3 5 7
    for (int x2 : s2) { cout << x2 << " "; } // 7 5 3 1
    return 0;
}
```

|成员|含义|
|-|-|
|`ret.first`|指向插入元素（或已存在元素）的**迭代器**|
|`ret.second`|**是否插入成功**：`true` 成功，`false` 已存在|

# 4.删除(erase)

- **`erase(value)`**：按值删除，返回删除个数（0 或 1）
- **`erase(pos)`**：通过迭代器删除，O(1)均摊
- **`erase(first, last)`**：删除区间 `[first, last)`

```C++
#include <iostream>
#include <set>
using namespace std;

int main() {
    set<int> s = {10, 20, 30, 40, 50};
    
    s.erase(30);
    auto it = s.begin();
    s.erase(it);
    
    auto first = s.find(40);
    auto last = s.find(50);
    if (first != s.end() && last != s.end()) {
        s.erase(first, last);
    }
    
    for (int x : s) { cout << x << " "; }
    return 0;
}
```

# 5.查找(find)

- **`find(value)`**：返回迭代器，没找到返回 `end()`
- **`count(value)`**：返回 0 或 1
- **`lower_bound(value)`**：第一个 >= value 的迭代器
- **`upper_bound(value)`**：第一个 > value 的迭代器

```C++
#include <iostream>
#include <set>
using namespace std;

int main() {
    set<int> s = {10, 20, 30, 40, 50};
    
    auto it = s.find(30);
    if (it != s.end()) cout << "找到了 30" << endl;
    
    if (s.count(20)) cout << "20 存在" << endl;
    
    auto low = s.lower_bound(25);
    auto up = s.upper_bound(45);
    cout << *low << " " << *up << endl; // 30 50
    return 0;
}
```

> `auto it = s.find(x)` 是 O(log N)，算法库的 `find(s.begin(),s.end(),x)` 是 O(N)

# 6.使用场景

`set` 适合需要**快速判断某个元素是否存在**且元素不能重复的场景。

|场景|例子|
|-|-|
|**去重**|统计有多少个不同的数|
|**判重**|判断一个数是否已出现过|
|**自动排序**|维护有序集合|
|**区间查找**|用 `lower_bound` / `upper_bound`|

```C++
vector<int> nums = {1, 2, 2, 3, 3, 3, 4};
set<int> s(nums.begin(), nums.end());
cout << s.size();  // 4

if (s.find(3) != s.end()) cout << "3 存在";
```

# 7.multiset和set的差异

|操作|`set`|`multiset`|
|-|-|-|
|插入相同值|❌ 无效|✅ 有效|
|`count(key)`|0 或 1|实际个数|
|`erase(key)`|删除唯一一个|**删除所有**匹配项|

```C++
// set
set<int> s;
s.insert(5); s.insert(3); s.insert(5);  // 无效
s.count(3);    // 1
s.erase(3);    // 删除 3

// multiset
multiset<int> ms;
ms.insert(5); ms.insert(3); ms.insert(5);  // 有效
ms.count(5);    // 2
ms.erase(5);    // 删除所有 5（2 个）
```

|需求|推荐容器|
|-|-|
|去重 + 自动排序|`set`|
|保留重复 + 自动排序|`multiset`|
|键值映射 + 自动排序|`map`|
|一个键对应多个值|`multimap`|
|只关心"存不存在"，不关心顺序|`unordered_set`|
|键值快速查找，不关心顺序|`unordered_map`|