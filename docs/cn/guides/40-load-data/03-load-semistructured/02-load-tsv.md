---
title: 将 TSV 数据导入 Databend
sidebar_label: TSV
---

## 什么是 TSV？

TSV (制表符分隔值) 是一种用于存储表格数据的简单文件格式，类似于电子表格或数据库。TSV 文件格式与 CSV 非常相似，记录由换行符分隔，每个字段由制表符分隔。
以下示例展示了一个包含两条记录的 TSV 文件：

```text
Title_0	Author_0
Title_1	Author_1
```

## 加载 TSV 文件

加载 TSV 文件的通用语法如下：

```sql
COPY INTO [<database>.]<table_name>
FROM { userStage | internalStage | externalStage | externalLocation }
[ PATTERN = '<regex_pattern>' ]
[ FILE_FORMAT = (
    TYPE = TSV,
    SKIP_HEADER = <integer>,
    COMPRESSION = AUTO
) ]
```

- 更多 TSV 文件格式选项，请参考 [TSV 文件格式选项](/sql/sql-reference/file-format-options#tsv-options) 。
- 更多 COPY INTO 表选项，请参考 [COPY INTO 表](/sql/sql-commands/dml/dml-copy-into-table) 。

## 教程：从 TSV 文件加载数据

### 步骤 1. 创建内部 Stage

创建一个内部 stage 来存储 TSV 文件。

```sql
CREATE STAGE my_tsv_stage;
```

### 步骤 2. 创建 TSV 文件

使用以下 SQL 语句生成 TSV 文件：

```sql
COPY INTO @my_tsv_stage
FROM (
    SELECT
        'Title_' || CAST(number AS VARCHAR) AS title,
        'Author_' || CAST(number AS VARCHAR) AS author
    FROM numbers(100000)
)
    FILE_FORMAT = (TYPE = TSV)
;
```

验证 TSV 文件是否创建成功：

```sql
LIST @my_tsv_stage;
```

结果：

```text
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                             name                            │   size  │                 md5                │         last_modified         │      creator     │
├─────────────────────────────────────────────────────────────┼─────────┼────────────────────────────────────┼───────────────────────────────┼──────────────────┤
│ data_7413d5d0-f992-4d92-b28e-0e501d66bdc1_0000_00000000.tsv │ 2477780 │ "a906769144de7aa6a0056a86ddae97d2" │ 2023-12-26 11:56:19.000 +0000 │ NULL             │
└───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

### 步骤 3：创建目标表

```sql
CREATE TABLE books
(
    title VARCHAR,
    author VARCHAR
);
```

### 步骤 4. 直接从 TSV 复制数据

使用以下 SQL 命令直接从 TSV 文件将数据复制到表中：

```sql
COPY INTO books
FROM @my_tsv_stage
PATTERN = '.*[.]tsv'
FILE_FORMAT = (
    TYPE = TSV,
    SKIP_HEADER = 0, -- 如果第一行是标题则跳过，这里我们没有标题行
    COMPRESSION = AUTO
);
```

结果：

```text
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                             File                            │ Rows_loaded │ Errors_seen │    First_error   │ First_error_line │
├─────────────────────────────────────────────────────────────┼─────────────┼─────────────┼──────────────────┼──────────────────┤
│ data_7413d5d0-f992-4d92-b28e-0e501d66bdc1_0000_00000000.tsv │      100000 │           0 │ NULL             │             NULL │
└───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

### 步骤 4 (可选). 使用 SELECT 语句复制数据

如需在复制时转换数据等更精细的控制，可以使用 SELECT 语句。了解更多请参阅 [`从 TSV 查询`](../04-transform/02-querying-tsv.md) 。

```sql
COPY INTO books (title, author)
FROM (
    SELECT $1, $2
    FROM @my_tsv_stage
)
PATTERN = '.*[.]tsv'
FILE_FORMAT = (
    TYPE = 'TSV',
    SKIP_HEADER = 0, -- 如果第一行是标题则跳过，这里我们没有标题行
    COMPRESSION = 'AUTO'
);
```