---
title: TRUNCATE
---

返回数值 `x` 截断到 `d` 位小数后的结果。若 `d` 为 0，则结果不包含小数点和小数部分。`d` 为负数时，会将 `x` 的小数点左侧 `d` 位数字置零。`d` 的最大绝对值为 30；若 `d` 大于 30 或小于 -30，则会被截断为 30 或 -30。

## 语法

```sql
TRUNCATE( <x, d> )
```

## 示例

```sql
SELECT TRUNCATE(1.223, 1);

┌────────────────────┐
│ truncate(1.223, 1) │
├────────────────────┤
│ 1.2                │
└────────────────────┘
```