# 一.三种插入数据方式

### 方法一：`insert` + `pair`（最标准）

```C++
#include <iostream>
#include <map>
using namespace std;

int main() {
    map<string, int> scores;
    
    // 方式1：用 make_pair
    scores.insert(make_pair("小谦", 100));
    
    // 方式2：用 {} 花括号
    scores.insert({"小明", 90});
    
    // 方式3：用 pair 构造函数
    scores.insert(pair<string, int>("小红", 85));
    
    // 遍历输出
    for (auto& p : scores) {
        cout << p.first << ": " << p.second << endl;
    }
    return 0;
}
```

---

### 方法二：`insert` + 检测是否插入成功（最常用，带判断）

```C++
#include <iostream>
#include <map>
using namespace std;

int main() {
    map<string, int> scores;
    
    auto ret = scores.insert({"小谦", 100});
    if (ret.second == false) {
        cout << "插入失败，该键已存在" << endl;
    } else {
        cout << "插入成功！" << endl;
    }

    ret = scores.insert({"小谦", 200});
    if (ret.second == false) {
        cout << "插入失败，键 '小谦' 已存在，值为: " << ret.first->second << endl;
    }
    return 0;
}
```

|成员|是什么|能不能判断插入是否成功？|为什么？|
|-|-|-|-|
|`ret.first`|**迭代器**，指向 `map`/`set` 中该键所在的位置|❌ **不能**|无论插入成功还是失败，它都指向一个有效位置，无法区分情况|
|`ret.second`|**bool 值**|✅ **唯一正确**|`true` 表示插入成功（键不存在），`false` 表示插入失败（键已存在）|

---

### 方法三：用 `[]` 下标操作（最简洁，但有隐患）

```C++
#include <iostream>
#include <map>
using namespace std;

int main() {
    map<string, int> scores;
    
    scores["小谦"] = 100;
    scores["小谦"] = 200;
    cout << scores["小谦"] << endl;  // 输出 200
    
    cout << scores["小刚"] << endl;  // 输出 0（int 的默认值），并且键 "小刚" 被插入了
    return 0;
}
```

# 二.一种更优雅的集初始化与插入为一体的写法

**初始化列表（现代写法）**

```C++
map<string, string> dict = {
    {"left", "左边"},
    {"right", "右边"},
    {"insert", "插入"}
};
```

在**功能上等价于**

```C++
map<string, string> dict;
dict.insert({"left", "左边"});
dict.insert({"right", "右边"});
dict.insert({"insert", "插入"});
```

# 三.map的删除(erase)

### `map::erase` 的三种形式

|重载|参数|返回值|说明|
|-|-|-|-|
|**1. 按键删除**|`size_type erase(const key_type& key)`|`size_t`（删除了几个元素）|最常用，直接传键名|
|**2. 按迭代器删除**|`iterator erase(const_iterator pos)`|`iterator`（指向被删元素的下一个）|遍历时边删边遍历的标配|
|**3. 按范围删除**|`iterator erase(const_iterator first, const_iterator last)`|`iterator`（指向 last）|删一段区间|

---

### 用法一：按键删除（最常用）

```C++
#include <iostream>
#include <map>
using namespace std;

int main() {
    map<string, string> dict = {
        {"left", "左边"},
        {"right", "右边"},
        {"insert", "插入"}
    };

    auto n = dict.erase("left");
    cout << "删除了 " << n << " 个元素" << endl;
    
    for (const auto& p : dict) {
        cout << p.first << ": " << p.second << endl;
    }
    return 0;
}
```

---

### 用法二：按迭代器删除（遍历时安全删除）

```C++
#include <iostream>
#include <map>
using namespace std;

int main() {
    map<string, string> dict = {
        {"left", "左边"},
        {"right", "右边"},
        {"insert", "插入"}
    };

    auto it = dict.begin();
    while (it != dict.end()) {
        if (it->second.find("边") != string::npos) {
            it = dict.erase(it);
        } else {
            ++it;
        }
    }

    for (const auto& p : dict) {
        cout << p.first << ": " << p.second << endl;
    }
    return 0;
}
```

### 拆解这行代码

```C++
if (it->second.find("边") != string::npos)
```

|部分|含义|
|-|-|
|`it`|指向 `map` 中一个键值对的迭代器|
|`it->second`|当前键值对的**值（value）**|
|`.find("边")`|在字符串中查找子串 `"边"`|
|`string::npos`|表示**"没找到"**|
|`!= string::npos`|说明找到了|

### ⚠️ 常见陷阱：遍历时删除

❌ **错误写法：**
```C++
for (auto it = dict.begin(); it != dict.end(); ++it) {
    if (it->second == "左边") {
        dict.erase(it);  // ❌ 迭代器失效
    }
}
```

✅ **正确写法：**
```C++
for (auto it = dict.begin(); it != dict.end(); ) {
    if (it->second == "左边") {
        it = dict.erase(it);
    } else {
        ++it;
    }
}
```

---

### 用法三：按范围删除（区间删除）

```C++
#include <iostream>
#include <map>
using namespace std;

int main() {
    map<string, string> dict = {
        {"a", "苹果"}, {"b", "香蕉"}, {"c", "橘子"},
        {"d", "葡萄"}, {"e", "西瓜"}
    };

    auto first = dict.find("b");
    auto last = dict.find("d");
    if (first != dict.end() && last != dict.end()) {
        dict.erase(first, last);
    }

    for (const auto& p : dict) {
        cout << p.first << ": " << p.second << endl;
    }
    return 0;
}
```

|概念|说明|
|-|-|
|**区间**|`[first, last)` 是**左闭右开**区间|
|**返回值**|返回指向 `last` 的迭代器|
|**复杂度**|O(n)|

# 四.用map统计一种水果出现的次数

### 写法一：`find` + 迭代器修改

```C++
#include <iostream>
#include <map>
#include <string>
using namespace std;

int main() {
    string arr[] = {"苹果", "西瓜", "苹果", "西瓜", "苹果", "苹果", "西瓜", "苹果", "香蕉", "香蕉"};
    map<string, int> countMap;

    for (const auto& str : arr) {
        auto ret = countMap.find(str);
        if (ret == countMap.end()) {
            countMap.insert({str, 1});
        } else {
            ret->second++;
        }
    }

    for (const auto& p : countMap) {
        cout << p.first << ": " << p.second << " 次" << endl;
    }
    return 0;
}
```

### 写法二：`[]` 下标写法（简洁版）

```C++
#include <iostream>
#include <map>
#include <string>
using namespace std;

int main() {
    string arr[] = {"苹果", "西瓜", "苹果", "西瓜", "苹果", "苹果", "西瓜", "苹果", "香蕉", "香蕉"};
    map<string, int> countMap;

    for (const auto& str : arr) {
        countMap[str]++;
    }

    for (const auto& p : countMap) {
        cout << p.first << ": " << p.second << " 次" << endl;
    }
    return 0;
}
```

## operator[] 底层细节（三个功能）

> **`operator[]` = "查找 + 按需插入"二合一。键存在就返回值的引用，键不存在就插入默认值再返回引用。**

### 1️⃣ 插入（Insert）
键不存在时自动插入默认值再返回引用。

### 2️⃣ 查找（Find / Access）
键存在时直接返回其值的引用。

### 3️⃣ 修改（Modify / Update）
通过返回的引用更新值。

### ⚠️ 重要警告
如果只是想**查找**键是否存在，**不要用 `[]`**，用 `find` 更安全。

### 💡 完整执行流程

```
1. 用 key 在 map 的红黑树中查找
   ├── 如果找到：直接返回该键对应值的引用
   └── 如果没找到：插入新键值对 {key, 默认值} → 返回新插入值的引用
2. 对返回的引用执行操作
```

# 五.map和multimap的区别

|对比维度|`map`|`multimap`|
|-|-|-|
|**键是否唯一**|✅ **唯一**|❌ **允许重复**|
|**`operator[]`**|✅ 支持|❌ **不支持**|
|**典型用途**|一一对应关系（姓名→分数）|一对多关系（班级→学生列表）|

# 六.map和pair容器的区别

|对比维度|`pair<string, int>`|`map<string, int>`|
|-|-|-|
|**是什么**|一个**单独的键值对**|**多个键值对的集合**|
|**包含元素**|**1 个**|**多个**（0 到任意多个）|

它们在代码中的关系：

```C++
// map 的每个元素就是 pair
for (const auto& p : countMap) {
    // p.first 是键，p.second 是值
}

// 把 map 中的内容转存到 vector<pair>
vector<pair<string, int>> vec;
for (const auto& p : countMap) {
    vec.push_back(p);
}

// pair 也可以独立存在
pair<string, int> single = {"香蕉", 2};
```