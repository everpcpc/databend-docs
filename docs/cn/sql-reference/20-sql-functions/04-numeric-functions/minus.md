---
title: MINUS
---

返回数值的负值。

## 语法

```sql
MINUS( <x> )
```

## 别名

- [NEG](neg.md)
- [NEGATE](negate.md)
- [SUBTRACT](subtract.md)

## 示例

```sql
SELECT MINUS(PI()), NEG(PI()), NEGATE(PI()), SUBTRACT(PI());

┌───────────────────────────────────────────────────────────────────────────────────┐
│     minus(pi())    │      neg(pi())     │    negate(pi())    │   subtract(pi())   │
├────────────────────┼────────────────────┼────────────────────┼────────────────────┤
│ -3.141592653589793 │ -3.141592653589793 │ -3.141592653589793 │ -3.141592653589793 │
└───────────────────────────────────────────────────────────────────────────────────┘
```