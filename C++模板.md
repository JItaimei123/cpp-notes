## C++ 函数模板核心知识点总结

### 1. 函数模板的基本格式

```C++
template<typename T1, typename T2, ...>
返回值类型 函数名(参数列表) {
    // 函数体
}
```

- `typename` 可以用 `class` 替换，但**不能用 `struct`**。

示例：

```C++
template<typename T>
void Swap(T& left, T& right) 
{
    T tmp = left;
    left = right;
    right = tmp;
}
```

---

### 2. 函数模板的实例化

实例化：用具体类型替换模板参数，生成真正的函数。

#### 2.1 隐式实例化

- 编译器根据实参类型**自动推导**模板参数。
- **要求**：所有实参类型必须一致，否则推导失败。

```C++
template<class T>
T Add(const T& left, const T& right) {
    return left + right;
}

int a = 10, b = 20;
double d1 = 10.0, d2 = 20.0;
Add(a, b);    // OK，T=int
Add(d1, d2);  // OK，T=double
Add(a, d1);   // ❌ 错误：一个T无法同时匹配 int 和 double
```

#### 2.2 显式实例化与推导实例化

- 在函数名后用 `<类型>` **显式指定**模板参数。
- 此时编译器会尝试进行隐式类型转换。

```C++
Add<int>(a, d1);   // OK，将 d1 转为 int，显式实例化
Add<double>(a, d1); // OK，将 a 转为 double，显式实例化
```

如果不想进行类型转化的话就要用另一个模板：

```C++
#include<iostream>
using std::cout;
using std::endl;
template<class T1, class T2>
auto Add(const T1& left, const T2& right)
{
    return left + right;
}
int main()
{
    int a1 = 10;
    double b1 = 10.2;
    cout << Add(a1, b1) << endl;
    return 0;
}
```

---

### 3. 模板参数的匹配原则（重载与优先级）

#### 3.1 同名普通函数与函数模板可以共存

```C++
int Add(int left, int right) {          // 普通函数
    return left + right;
}

template<class T>
T Add(T left, T right) {                // 函数模板
    return left + right;
}
```

#### 3.2 调用时的选择规则

- **优先匹配普通函数**（非模板函数），如果类型完全匹配。
- 只有当普通函数不匹配，或模板能产生**更好的匹配**时，才会使用模板。

```C++
Add(1, 2);        // 调用普通函数（int, int）
Add<int>(1, 2);   // 显式指定模板，调用模板实例
```

#### 3.3 类型转换的差异

- **普通函数**：允许隐式类型转换（如 `int` → `double`）。
- **函数模板**：不允许隐式类型转换，要求严格匹配。

```C++
Add(1, 2.0);      // 普通函数存在时：调用普通函数（允许转换）
Add<int>(1, 2.0); // 模板：显式指定，2.0 转为 int
```

---

### 4. 一句话记忆

> **普通函数优先，且支持隐式转换；函数模板要求严格类型匹配，可以用 `<>` 强制指定类型。**

---

### 5. 有模板有普通函数先运行谁？

```C++
#include <iostream>
using namespace std;
template<class T>
T Add(const T& left, const T& right) 
{
    cout << "模板" << endl;
    return left + right;
}

int Add(const int& x, const int& y)
{
    cout << "普通函数" << endl;
    return (x + y) * 10;
}

int main() 
{
    int a = 1, b = 2;
    cout << Add(a, b) << endl;   // 调用普通函数，输出 30
    cout << Add<>(a, b) << endl; // 显式实例化，强制调用模板，输出 3
    return 0;
}
```