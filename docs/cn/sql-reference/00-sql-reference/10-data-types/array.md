---
title: Array(T)
description: 定义数据类型的数组。
---

## Array(T) 数据类型

ARRAY(T) 由定义的可变长度内部 T 数据类型值组成，这与半结构化的 ARRAY 非常相似，不同之处在于需要定义内部数据类型，而不是任意类型。T 可以是任何数据类型。

:::note
Databend 对数组使用从 1 开始的编号约定。一个包含 n 个元素的数组从 array[1] 开始，到 array[n] 结束。
:::

### 示例

```sql
CREATE TABLE array_int64_table(arr ARRAY(INT64));
```

```sql
DESC array_int64_table;
```

结果：

```
┌───────────────────────────────────────────────────┐
│  Field │     Type     │  Null  │ Default │  Extra │
├────────┼──────────────┼────────┼─────────┼────────┤
│ arr    │ ARRAY(INT64) │ YES    │ NULL    │        │
└────────┼──────────────┼────────┼─────────┼────────┤
```

```sql
-- 将数组值插入到表中
INSERT INTO array_int64_table
VALUES
([1, 2, 3, 4]),
([5, 6, 7, 8]);
```

```sql
SELECT arr FROM array_int64_table;
```

结果：

```
+-----------+
| arr       |
+-----------+
| [1,2,3,4] |
| [5,6,7,8] |
+-----------+
```

```sql
-- 从表中选择 'arr' 数组的第零个元素
SELECT arr[0]
FROM array_int64_table;
```

结果：

```
┌─────────────────┐
│      arr[0]     │
├─────────────────┤
│            NULL │
│            NULL │
└─────────────────┘
```