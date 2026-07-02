# 一.仿函数是什么？

- **本质**：一个**类**，这个类重载了 `operator()`。
- **效果**：这个类的对象可以**像函数一样被调用**（即 `对象名(参数)` 的形式）。

### 代码示例

```C++
template<class T>
class Less {
public:
    bool operator()(const T& x, const T& y) { return x < y; }
};

int main() {
    Less<int> LessFunc;
    cout << LessFunc(1, 2) << endl;
    cout << LessFunc.operator()(1, 2) << endl;
    return 0;
}
```

```C++
template<class T>
class Greater {
public:
    bool operator()(const T& x, const T& y) { return x > y; }
};

int main() {
    Greater<int> GreaterFunc;
    cout << GreaterFunc(1, 2) << endl;
    cout << GreaterFunc.operator()(1, 2) << endl;
    return 0;
}
```

# 二.仿函数有什么用？

- 替代函数指针，更灵活、安全。
- 常用于 STL 算法（如 `sort`）和容器（如 `priority_queue`）自定义比较规则。

> **仿函数 = 类 + 重载 `operator()`，让对象能当函数用。**

# 三.结合仿函数写冒泡排序

```C++
#include <iostream>
using namespace std;

template<class T>
class Less {
public:
    bool operator()(const T& x, const T& y) { return x < y; }
};

template<class T>
class Greater {
public:
    bool operator()(const T& x, const T& y) { return x > y; }
};

template<class T, class Compare>
void BubbleSort(T* arr, int n, Compare comp) {
    for (int i = 0; i < n - 1; i++) {
        bool swapped = false;
        for (int j = 0; j < n - i - 1; j++) {
            if (comp(arr[j + 1], arr[j])) {
                swap(arr[j], arr[j + 1]);
                swapped = true;
            }
        }
        if (!swapped) break;
    }
}

int main() {
    double arr1[] = { 5.1, 2.2, 8.3, 1.9, 9.1 };
    int n = sizeof(arr1) / sizeof(arr1[0]);
    Less<double> LessFunc;
    BubbleSort(arr1, n, LessFunc);
    for (auto x : arr1) cout << x << " ";
    cout << endl;

    double arr2[] = { 5.1, 2.2, 8.3, 1.9, 9.1 };
    Greater<double> GreaterFunc;
    BubbleSort(arr2, n, GreaterFunc);
    for (auto x : arr2) cout << x << " ";
    cout << endl;
    return 0;
}
```

得益于模板 `T`，可对 `double`、`int` 等任意类型排序。

# 四.sort的基本使用

## 1、基本用法（默认升序）

```C++
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

int main() {
    int arr[] = {5, 2, 8, 1, 9};
    int n = sizeof(arr) / sizeof(arr[0]);
    sort(arr, arr + n);
    for (int x : arr) cout << x << " "; // 1 2 5 8 9
    cout << endl;

    vector<int> v = {5, 2, 8, 1, 9};
    sort(v.begin(), v.end());
    for (int x : v) cout << x << " "; // 1 2 5 8 9
    cout << endl;
    return 0;
}
```

## 2、降序排序（用 `greater<int>()`）

```C++
#include <iostream>
#include <algorithm>
#include <functional>
#include <vector>
using namespace std;

int main() {
    vector<int> v = {5, 2, 8, 1, 9};
    sort(v.begin(), v.end(), greater<int>());
    for (int x : v) cout << x << " "; // 9 8 5 2 1
    return 0;
}
```

## 3、自定义排序（用仿函数 / lambda）

### 3.1 用仿函数

```C++
struct Compare {
    bool operator()(int a, int b) const { return a > b; }
};
sort(v.begin(), v.end(), Compare());
```

### 3.2 用 lambda（竞赛最常用）

```C++
sort(v.begin(), v.end(), [](int a, int b) { return a > b; });
```

## 4、对自定义类型排序

```C++
struct Student { string name; int score; };
vector<Student> v = {{"张三", 85}, {"李四", 92}, {"王五", 78}};

sort(v.begin(), v.end(), [](const Student& a, const Student& b) {
    return a.score < b.score;
});
```

## 5、总结表格

|用法|代码|
|-|-|
|升序（默认）|`sort(v.begin(), v.end())`|
|降序|`sort(v.begin(), v.end(), greater<int>())`|
|自定义（lambda）|`sort(v.begin(), v.end(), [](int a, int b){ return a > b; })`|
|数组排序|`sort(arr, arr + n)`|

# 六.lambda的基本用法

## 一、为什么需要 lambda？

以前你要自定义排序规则，必须单独写一个仿函数类：

```C++
struct Compare {
    bool operator()(int a, int b) const { return a > b; }
};
sort(v.begin(), v.end(), Compare());
```

lambda 允许你**直接写在调用处**：

```C++
sort(v.begin(), v.end(), [](int a, int b) { return a > b; });
```

## 二、lambda 的基本语法

```C++
[capture](parameters) -> return_type { body }
```

|部分|含义|是否可省略|
|-|-|-|
|`[capture]`|捕获列表|❌ 不可省略|
|`(parameters)`|参数列表|✅ 可省略|
|`-> return_type`|返回类型|✅ 可省略|
|`{ body }`|函数体|❌ 不可省略|

## 三、lambda 示例

```C++
auto add = [](int a, int b) { return a + b; };
cout << add(3, 5); // 8

vector<int> v = {5, 2, 8, 1, 9};
sort(v.begin(), v.end(), [](int a, int b) { return a > b; });
```

## 四、捕获列表

```C++
int threshold = 10;
vector<int> v = {5, 15, 8, 20};
auto it = remove_if(v.begin(), v.end(), [threshold](int x) { return x < threshold; });
```

- `[=]`：值捕获所有外部变量
- `[&]`：引用捕获所有外部变量
- `[x, &y]`：值捕获 x，引用捕获 y

## 五、lambda vs 仿函数

|对比|lambda|仿函数|
|-|-|-|
|代码长度|短|长|
|复用性|不能复用|可以多次使用|

> **lambda 就是临时写的、用完即扔的函数。**
