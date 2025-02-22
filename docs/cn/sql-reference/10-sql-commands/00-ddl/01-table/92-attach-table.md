---
title: ATTACH TABLE
sidebar_position: 6
---

import FunctionDescription from '@site/src/components/FunctionDescription';

<FunctionDescription description="引入或更新于：v1.2.549"/>

import EEFeature from '@site/src/components/EEFeature';

<EEFeature featureName='ATTACH TABLE'/>

将现有表附加到另一个表。该命令将表的数据和模式从一个数据库移动到另一个数据库，但实际上并不复制数据。相反，它创建一个指向原始表数据的链接以访问数据。

Attach Table 使您能够无缝地将云服务平台中的表连接到私有部署环境中已存在的表，而无需物理移动数据。这在您希望将数据从私有部署的 Databend 迁移到 [Databend Cloud](https://www.databend.com) 时特别有用，同时最大限度地减少数据传输开销。

附加的表以 READ_ONLY 模式运行。在此模式下，源表中的更改会立即反映在附加表中。然而，附加表仅用于查询目的，不支持更新。这意味着在附加表上不允许执行 INSERT、UPDATE 和 DELETE 操作；只能执行 SELECT 查询。

## 语法

```sql
ATTACH TABLE <target_table_name> '<source_table_data_URI>'
CONNECTION = ( <connection_parameters> )
```

- `<source_table_data_URI>` 表示源表数据的路径。对于类似 S3 的对象存储，格式为 `s3://<bucket-name>/<database_ID>/<table_ID>`，例如 _s3://databend-toronto/1/23351/_，它表示存储桶中表文件夹的确切路径。

  ![Alt text](/img/sql/attach.png)

  要获取表的数据库 ID 和表 ID，请使用 [FUSE_SNAPSHOT](../../../20-sql-functions/16-system-functions/fuse_snapshot.md) 函数。在下面的示例中，_snapshot_location_ 值中的 **1/23351/** 部分表示数据库 ID 为 **1**，表 ID 为 **23351**。

  ```sql
  SELECT * FROM FUSE_SNAPSHOT('default', 'employees');

  Name                |Value                                              |
  --------------------+---------------------------------------------------+
  snapshot_id         |d6cd1f3afc3f4ad4af298ad94711ead1                   |
  snapshot_location   |1/23351/_ss/d6cd1f3afc3f4ad4af298ad94711ead1_v4.mpk|
  format_version      |4                                                  |
  previous_snapshot_id|                                                   |
  segment_count       |1                                                  |
  block_count         |1                                                  |
  row_count           |3                                                  |
  bytes_uncompressed  |122                                                |
  bytes_compressed    |523                                                |
  index_size          |470                                                |
  timestamp           |2023-07-11 05:38:27.0                              |
  ```

- `CONNECTION` 指定建立与存储源表数据的对象存储链接所需的连接参数。不同存储服务的连接参数根据其特定要求和认证机制而有所不同。有关更多信息，请参见 [连接参数](../../../00-sql-reference/51-connect-parameters.md)。

## 示例

本示例展示了如何将 Databend Cloud 中的新表与 Databend 中的现有表链接，该表将数据存储在名为 "databend-toronto" 的 Amazon S3 存储桶中。

#### 步骤 1. 在 Databend 中创建表

创建一个名为 "population" 的表并插入一些示例数据：

```sql title='Databend:'
CREATE TABLE population (
  city VARCHAR(50),
  population INT
);

INSERT INTO population (city, population) VALUES
  ('Toronto', 2731571),
  ('Montreal', 1704694),
  ('Vancouver', 631486);
```

#### 步骤 2. 获取数据库 ID 和表 ID

使用 [FUSE_SNAPSHOT](../../../20-sql-functions/16-system-functions/fuse_snapshot.md) 函数获取数据库 ID 和表 ID。以下结果表明数据库 ID 为 **1**，表 ID 为 **556**：

```sql title='Databend:'
SELECT * FROM FUSE_SNAPSHOT('default', 'population');

┌──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│            snapshot_id           │                 snapshot_location                 │ format_version │ previous_snapshot_id │ segment_count │ block_count │ row_count │ bytes_uncompressed │ bytes_compressed │ index_size │          timestamp         │
├──────────────────────────────────┼───────────────────────────────────────────────────┼────────────────┼──────────────────────┼───────────────┼─────────────┼───────────┼────────────────────┼──────────────────┼────────────┼────────────────────────────┤
│ f252dd43d1aa44898a04827808342daf │ 1/556/_ss/f252dd43d1aa44898a04827808342daf_v4.mpk │              4 │ NULL                 │             1 │           1 │         3 │                 70 │              448 │        531 │ 2023-11-01 02:35:47.325319 │
└──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

当您访问 Amazon S3 上的存储桶页面时，您会观察到数据被组织在路径 `databend-toronto` > `1` > `556` 中，如下所示：

![Alt text](/img/sql/attach-table-2.png)

#### 步骤 3. 在 Databend Cloud 中链接表

登录 Databend Cloud 并在工作表中运行以下命令以链接名为 "population_readonly" 的表：

```sql title='Databend Cloud:'
ATTACH TABLE population_readonly 's3://databend-toronto/1/556/' CONNECTION = (
  AWS_KEY_ID = '<your_aws_key_id>',
  AWS_SECRET_KEY = '<your_aws_secret_key>'
);
```

要验证链接是否成功，请在 Databend Cloud 中运行以下查询：

```sql title='Databend Cloud:'
SELECT * FROM population_readonly;

-- 预期结果：
┌────────────────────────────────────┐
│       city       │    population   │
├──────────────────┼─────────────────┤
│ Toronto          │         2731571 │
│ Montreal         │         1704694 │
│ Vancouver        │          631486 │
└────────────────────────────────────┘
```

一切就绪！如果您在 Databend 中更新源表，您可以在 Databend Cloud 中的目标表中观察到相同的更改。例如，如果您将源表中 Toronto 的人口更改为 2,371,571：

```sql title='Databend:'
UPDATE population
SET population = 2371571
WHERE city = 'Toronto';
```

您可以看到更新已同步到 Databend Cloud 中的附加表：

```sql title='Databend Cloud:'
SELECT * FROM population_readonly;

-- 预期结果：
┌────────────────────────────────────┐
│       city       │    population   │
├──────────────────┼─────────────────┤
│ Toronto          │         2371571 │
│ Montreal         │         1704694 │
│ Vancouver        │          631486 │
└────────────────────────────────────┘
```