---
title: TO_MICROSECONDS
---
import FunctionDescription from '@site/src/components/FunctionDescription';

<FunctionDescription description="引入或更新于：v1.2.677"/>

将指定的微秒数转换为 Interval 类型。

- 接受正整数、零和负整数作为输入。

## 语法

```sql
TO_MICROSECONDS(<microseconds>)
```

## 返回类型

Interval（格式为 `hh:mm:ss.sssssss`）。

## 示例

```sql
SELECT TO_MICROSECONDS(2), TO_MICROSECONDS(0), TO_MICROSECONDS((- 2));

┌────────────────────────────────────────────────────────────────┐
│ to_microseconds(2) │ to_microseconds(0) │ to_microseconds(- 2) │
├────────────────────┼────────────────────┼──────────────────────┤
│ 0:00:00.000002     │ 00:00:00           │ -0:00:00.000002      │
└────────────────────────────────────────────────────────────────┘
```