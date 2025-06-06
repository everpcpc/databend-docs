---
title: 'L2_DISTANCE'
description: '在 Databend 中测量向量之间的欧几里得距离'
---

计算两个向量之间的欧几里得（L2）距离，测量它们在向量空间中的直线距离。

## 语法

```sql
L2_DISTANCE(vector1, vector2)
```

## 参数

- `vector1`: 第一个向量 (ARRAY(FLOAT NOT NULL))
- `vector2`: 第二个向量 (ARRAY(FLOAT NOT NULL))

## 返回值

返回一个 FLOAT 值，表示两个向量之间的欧几里得（L2）距离。该值始终为非负数：
- 0：相同的向量
- 较大的值：距离较远的向量

## 描述

L2 距离，也称为欧几里得距离，测量欧几里得空间中两点之间的直线距离。它是向量相似性搜索和机器学习应用中最常用的指标之一。

该函数：

1. 验证两个输入向量是否具有相同的长度
2. 计算对应元素之间平方差的总和
3. 返回该总和的平方根

实现的数学公式为：

```
L2_distance(v1, v2) = √(Σ(v1ᵢ - v2ᵢ)²)
```

其中 v1ᵢ 和 v2ᵢ 是输入向量的元素。

:::info
- 此函数在 Databend 中执行向量计算，不依赖于外部 API。
:::

## 示例

创建一个包含向量数据的表：

```sql
CREATE OR REPLACE TABLE vectors (
    id INT,
    vec ARRAY(FLOAT NOT NULL)
);

INSERT INTO vectors VALUES
    (1, [1.0000, 2.0000, 3.0000]),
    (2, [1.0000, 2.2000, 3.0000]),
    (3, [4.0000, 5.0000, 6.0000]);
```

使用 L2 距离查找最接近 [1, 2, 3] 的向量：

```sql
SELECT 
    id,
    vec, 
    L2_DISTANCE(vec, [1.0000, 2.0000, 3.0000]) AS distance
FROM 
    vectors
ORDER BY 
    distance ASC;
```

```
+----+-------------------------+----------+
| id | vec                     | distance |
+----+-------------------------+----------+
| 1  | [1.0000,2.0000,3.0000]  | 0.0      |
| 2  | [1.0000,2.2000,3.0000]  | 0.2      |
| 3  | [4.0000,5.0000,6.0000]  | 5.196152 |
+----+-------------------------+----------+
```