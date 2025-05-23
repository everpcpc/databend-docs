---
title: INSPECT_PARQUET
---
import FunctionDescription from '@site/src/components/FunctionDescription';

<FunctionDescription description="引入或更新于：v1.2.180"/>

从暂存的 Parquet 文件中检索包含以下列的全面元数据表：

| 列名                           | 描述                                                    |
|----------------------------------|----------------------------------------------------------------|
| created_by                       | 负责创建 Parquet 文件的实体或来源 |
| num_columns                      | Parquet 文件中的列数                      |
| num_rows                         | Parquet 文件中的总行数或记录数        |
| num_row_groups                   | Parquet 文件中的行组数                |
| serialized_size                  | Parquet 文件在磁盘上的大小（压缩后）              |
| max_row_groups_size_compressed   | 最大行组的大小（压缩后）                 |
| max_row_groups_size_uncompressed | 最大行组的大小（未压缩）               |

## 语法

```sql
INSPECT_PARQUET('@<文件路径>')
```

## 示例

此示例从暂存的名为 [books.parquet](https://datafuse-1253727613.cos.ap-hongkong.myqcloud.com/data/books.parquet) 的示例 Parquet 文件中检索元数据。该文件包含两条记录：

```text title='books.parquet'
Transaction Processing,Jim Gray,1992
Readings in Database Systems,Michael Stonebraker,2004
```

```sql
-- 显示暂存的文件
LIST @my_internal_stage;

┌──────────────────────────────────────────────────────────────────────────────────────────────┐
│      name     │  size  │        md5       │         last_modified         │      creator     │
├───────────────┼────────┼──────────────────┼───────────────────────────────┼──────────────────┤
│ books.parquet │    998 │ NULL             │ 2023-04-19 19:34:51.303 +0000 │ NULL             │
└──────────────────────────────────────────────────────────────────────────────────────────────┘

-- 从暂存文件中检索元数据
SELECT * FROM INSPECT_PARQUET('@my_internal_stage/books.parquet');

┌────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│             created_by             │ num_columns │ num_rows │ num_row_groups │ serialized_size │ max_row_groups_size_compressed │ max_row_groups_size_uncompressed │
├────────────────────────────────────┼─────────────┼──────────┼────────────────┼─────────────────┼────────────────────────────────┼──────────────────────────────────┤
│ parquet-cpp version 1.5.1-SNAPSHOT │           3 │        2 │              1 │             998 │                            332 │                              320 │
└────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```