---
title: SIGN
---

返回参数的符号：若 `x` 为负数、零或正数，则分别返回 -1、0 或 1；若参数为 NULL，则返回 NULL。

## 语法

```sql
SIGN( <x> )
```

## 示例

```sql
SELECT SIGN(0);

┌─────────┐
│ sign(0) │
├─────────┤
│       0 │
└─────────┘
```