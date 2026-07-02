# 一.结合模版实现一个栈

```C++
#include <deque>
#include <vector>
#include <iostream>
namespace bit {
    template<class T, class Container = std::deque<T>>
    class stack {
    private:
        Container _con;
    public:
        void push(const T& val) { _con.push_back(val); }
        void pop() { _con.pop_back(); }
        T& top() { return _con.back(); }
        const T& top() const { return _con.back(); }
        bool empty() const { return _con.empty(); }
        size_t size() const { return _con.size(); }
    };
}
```

调用：

```C++
#include "stack.h"
bit::stack<int, vector<int>> st;
```

# 二.结合模板实现一个队列

```C++
#include <iostream>
#include <deque>
#include <list>
using namespace std;

namespace my {
    template <class T, class Container = std::deque<T>>
    class queue {
    private:
        Container _con;
    public:
        void push(const T& val) { _con.push_back(val); }
        void pop() { _con.pop_front(); }
        T& front() { return _con.front(); }
        const T& front() const { return _con.front(); }
        T& back() { return _con.back(); }
        const T& back() const { return _con.back(); }
        bool empty() const { return _con.empty(); }
        size_t size() const { return _con.size(); }
    };
}
```

# 三.priority_queue的基本操作

### 1. 基本操作（大顶堆）

```C++
#include <iostream>
#include <queue>
using namespace std;

int main() {
    priority_queue<int> pq;
    pq.push(5); pq.push(1); pq.push(10); pq.push(3);

    cout << "堆顶: " << pq.top() << endl; // 10
    pq.pop();
    cout << "删除后堆顶: " << pq.top() << endl; // 5

    while (!pq.empty()) { cout << pq.top() << " "; pq.pop(); }
    return 0;
}
```

### 2. 小顶堆（竞赛高频）

```C++
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

int main() {
    priority_queue<int, vector<int>, greater<int>> pq;
    pq.push(5); pq.push(1); pq.push(10); pq.push(3);

    cout << "小顶堆堆顶: " << pq.top() << endl; // 1
    while (!pq.empty()) { cout << pq.top() << " "; pq.pop(); }
    return 0;
}
```

`greater<int>` 是标准库的仿函数，判断 `a > b`。

### 3. 自定义类型放入优先队列

```C++
struct Student {
    string name;
    int score;
    bool operator<(const Student& other) const {
        return score < other.score;
    }
};

priority_queue<Student> pq;
pq.push({"张三", 85}); pq.push({"李四", 92}); pq.push({"王五", 78});
while (!pq.empty()) {
    Student s = pq.top();
    cout << s.name << ":" << s.score << endl;
    pq.pop();
}
```

### 4. 自定义比较方式（用仿函数）

```C++
struct CompareByAge {
    bool operator()(const Person& a, const Person& b) const {
        return a.age > b.age;
    }
};

priority_queue<Person, vector<Person>, CompareByAge> pq;
```
