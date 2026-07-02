## 10. map与set区分总结

### `set` vs `map` 选择指南

|需求|用什么|
|-|-|
|只需要知道"有没有出现过"|`set`|
|需要统计"出现了多少次"|`map`|
|需要把某个值和另一个值绑定|`map`|
|只需要去重、自动排序|`set`|
|需要快速查找（不关心顺序）|`unordered_set` / `unordered_map`（哈希表，O(1)）|

### 实际刷题中的应用

|题目类型|常用容器|
|-|-|
|快乐数（判断循环）|`unordered_set`|
|两数之和|`unordered_map`|
|统计频率|`map` / `unordered_map`|
|合并区间|`set` 或 `map`|
|找到第一个重复字符|`set` / `unordered_set`|
|前 K 个高频单词|`map` + `priority_queue`|
|自动排序去重|`set`|

### 一句话总结

> **`set` 回答"有没有"，`map` 回答"有几个"或"对应什么"。**

---