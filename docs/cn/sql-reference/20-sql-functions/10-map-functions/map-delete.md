---
title: MAP_DELETE
---
import FunctionDescription from '@site/src/components/FunctionDescription';

<FunctionDescription description="引入或更新于：v1.2.547"/>

返回一个移除了一个或多个键的现有 MAP。

## 语法

```sql
MAP_DELETE( <map>, <key1> [, <key2>, ... ] )
MAP_DELETE( <map>, <array> )
```

## 参数

| 参数      | 描述                                            |
|-----------|------------------------------------------------|
| `<map>`   | 包含要移除的键的 MAP。                          |
| `<keyN>`  | 要从返回的 MAP 中省略的键。                     |
| `<array>` | 要从返回的 MAP 中省略的键的数组。               |

:::note
- 键表达式的类型和 MAP 中的键的类型必须相同。
- 在 MAP 中未找到的键值将被忽略。
:::

## 返回类型

Map。

## 示例

```sql
SELECT MAP_DELETE({'a':1,'b':2,'c':3}, 'a', 'c');
┌───────────────────────────────────────────┐
│ map_delete({'a':1,'b':2,'c':3}, 'a', 'c') │
├───────────────────────────────────────────┤
│ {'b':2}                                   │
└───────────────────────────────────────────┘

SELECT MAP_DELETE({'a':1,'b':2,'c':3}, ['a', 'b']);
┌─────────────────────────────────────────────┐
│ map_delete({'a':1,'b':2,'c':3}, ['a', 'b']) │
├─────────────────────────────────────────────┤
│ {'c':3}                                     │
└─────────────────────────────────────────────┘
```