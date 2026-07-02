### `unordered_set` vs `unordered_set`

|特性|`unordered_set`|`unordered_map`|
|-|-|-|
|**存储内容**|只存储 **键（Key）**|存储 **键值对（Key-Value）**|
|**元素唯一性**|键唯一，不允许重复|键唯一，不允许重复|
|**访问方式**|只能判断元素在不在|通过键访问对应的值|
|**类比**|一个**盒子**，只能看东西在不在里面|一个**字典**，通过单词查释义|
|**典型用途**|去重、快速查找是否存在|统计频率、建立映射关系|

---

### `unordered_set` 详解

**特点**：只存键，不存值，用于快速判断某个元素是否存在。

```C++
#include <iostream>
#include <unordered_set>
using namespace std;

int main() {
    unordered_set<int> s;

    // 插入元素
    s.insert(5);
    s.insert(3);
    s.insert(7);

    // 查找元素
    if (s.find(5) != s.end()) {
        cout << "5 存在" << endl;  // ✅ 输出
    }

    // 遍历（无序！）
    for (int x : s) {
        cout << x << " ";  // 可能输出 3 5 7（顺序不确定）
    }

    return 0;
}
```


**常用操作**：

|操作|代码|时间复杂度|
|-|-|-|
|插入|`s.insert(x)`|O(1) 平均|
|查找|`s.find(x) != s.end()`|O(1) 平均|
|删除|`s.erase(x)`|O(1) 平均|
|大小|`s.size()`|O(1)|

---

### `unordered_map` 详解

**特点**：存储键值对，通过键快速访问对应的值。

```C++
#include <iostream>
#include <unordered_map>
using namespace std;

int main() {
    unordered_map<string, int> m;

    // 插入键值对
    m["苹果"] = 5;
    m["西瓜"] = 3;
    m.insert({"香蕉", 2});

    // 查找并访问值
    if (m.find("苹果") != m.end()) {
        cout << "苹果: " << m["苹果"] << " 个" << endl;  // 输出 5
    }

    // 遍历（无序！）
    for (const auto& p : m) {
        cout << p.first << ": " << p.second << endl;
    }

    return 0;
}
```


**常用操作**：

|操作|代码|时间复杂度|
|-|-|-|
|插入|`m[key] = value` 或 `m.insert({key, value})`|O(1) 平均|
|查找|`m.find(key) != m.end()`|O(1) 平均|
|访问值|`m[key]`（键不存在会插入默认值）|O(1) 平均|
|删除|`m.erase(key)`|O(1) 平均|

---

### `unordered_set` vs `set` 对比

|对比维度|`unordered_set`|`set`|
|-|-|-|
|**底层实现**|哈希表|红黑树（平衡二叉搜索树）|
|**元素顺序**|❌ 无序|✅ 有序（升序）|
|**查找复杂度**|O(1) 平均|O(log n)|
|**插入复杂度**|O(1) 平均|O(log n)|
|**空间占用**|较大（哈希表开销）|较小|
|**适用场景**|只需要快速查找，不关心顺序|需要有序遍历，或需要范围查询|
|**迭代器**|单向迭代器|双向迭代器|

---

### `unordered_map` vs `map` 对比

|对比维度|`unordered_map`|`map`|
|-|-|-|
|**底层实现**|哈希表|红黑树（平衡二叉搜索树）|
|**键的顺序**|❌ 无序|✅ 有序（升序）|
|**查找复杂度**|O(1) 平均|O(log n)|
|**插入复杂度**|O(1) 平均|O(log n)|
|**空间占用**|较大|较小|
|**适用场景**|快速查找，不关心顺序|需要有序键，或范围查询|
|**迭代器**|单项迭代器|双向迭代器|