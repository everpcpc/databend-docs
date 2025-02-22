---
title: PARSE_JSON
description:
  解释输入的JSON字符串，生成一个VARIANT值
title_includes: TRY_PARSE_JSON
---

`parse_json` 和 `try_parse_json` 将输入字符串解释为JSON文档，生成一个VARIANT值。

`try_parse_json` 在解析过程中发生错误时返回NULL值。

## 语法

```sql
PARSE_JSON(<expr>)
TRY_PARSE_JSON(<expr>)
```

## 参数

| 参数      | 描述                                                                         |
|-----------|------------------------------------------------------------------------------|
| `<expr>`  | 一个字符串类型的表达式（例如VARCHAR），包含有效的JSON信息。                   |

## 返回类型

VARIANT

## 示例

```sql
SELECT parse_json('[-1, 12, 289, 2188, false]');
+------------------------------------------+
| parse_json('[-1, 12, 289, 2188, false]') |
+------------------------------------------+
| [-1,12,289,2188,false]                   |
+------------------------------------------+

SELECT try_parse_json('{ "x" : "abc", "y" : false, "z": 10} ');
+---------------------------------------------------------+
| try_parse_json('{ "x" : "abc", "y" : false, "z": 10} ') |
+---------------------------------------------------------+
| {"x":"abc","y":false,"z":10}                            |
+---------------------------------------------------------+
```