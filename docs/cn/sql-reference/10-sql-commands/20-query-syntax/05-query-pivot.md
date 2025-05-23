---
title: PIVOT
---

Databend 中的 `PIVOT` 操作允许您通过旋转表并根据指定的列聚合结果来转换表。

对于以更易读的格式汇总和分析大量数据，这是一个有用的操作。在本文档中，我们将解释语法并提供如何使用 `PIVOT` 操作的示例。

**参见：**
[UNPIVOT](./05-query-unpivot.md)

## 语法

```sql
SELECT ...
FROM ...
   PIVOT ( <aggregate_function> ( <pivot_column> )
            FOR <value_column> IN ( <pivot_value_1> [ , <pivot_value_2> ... ] ) )

[ ... ]
```

其中：

- `<aggregate_function>`：用于组合来自 `pivot_column` 的分组值的聚合函数。
- `<pivot_column>`：将使用指定的 `<aggregate_function>` 聚合的列。
- `<value_column>`：其唯一值将成为透视结果集中的新列的列。
- `<pivot_value_N>`：来自 `<value_column>` 的唯一值，它将成为透视结果集中的新列。

## 示例

假设我们有一个名为 monthly_sales 的表，其中包含不同员工在不同月份的销售数据。我们可以使用 `PIVOT` 操作来汇总数据并计算每个员工在每个月的总销售额。

### 创建和插入数据

```sql
-- 创建 monthly_sales 表
CREATE TABLE monthly_sales(
  empid INT,
  amount INT,
  month VARCHAR
);

-- 插入销售数据
INSERT INTO monthly_sales VALUES
  (1, 10000, 'JAN'),
  (1, 400, 'JAN'),
  (2, 4500, 'JAN'),
  (2, 35000, 'JAN'),
  (1, 5000, 'FEB'),
  (1, 3000, 'FEB'),
  (2, 200, 'FEB'),
  (2, 90500, 'FEB'),
  (1, 6000, 'MAR'),
  (1, 5000, 'MAR'),
  (2, 2500, 'MAR'),
  (2, 9500, 'MAR'),
  (1, 8000, 'APR'),
  (1, 10000, 'APR'),
  (2, 800, 'APR'),
  (2, 4500, 'APR');
```

### 使用 PIVOT

现在，我们可以使用 `PIVOT` 操作来计算每个员工在每个月的总销售额。我们将使用 `SUM` 聚合函数来计算总销售额，并且 MONTH 列将被透视以创建每个月的新列。

```sql
SELECT *
FROM monthly_sales
PIVOT(SUM(amount) FOR MONTH IN ('JAN', 'FEB', 'MAR', 'APR'))
ORDER BY EMPID;
```

输出：

```sql
+-------+-------+-------+-------+-------+
| empid | jan   | feb   | mar   | apr   |
+-------+-------+-------+-------+-------+
|     1 | 10400 |  8000 | 11000 | 18000 |
|     2 | 39500 | 90700 | 12000 |  5300 |
+-------+-------+-------+-------+-------+
```
