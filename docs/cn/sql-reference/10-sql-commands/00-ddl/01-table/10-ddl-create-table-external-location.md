---
title: CREATE EXTERNAL TABLE
sidebar_position: 2
---

`CREATE TABLE ... CONNECTION = (...)` 语句用于创建表，并指定一个与 S3 兼容的存储桶用于数据存储，而不是使用默认的本地存储。

然后，fuse table engine 表将被存储在指定的 S3 兼容存储桶中。

## 优势

- 您可以确定表数据的存储位置。
- 利用高性能存储，如 [Amazon S3 Express One Zone](https://aws.amazon.com/s3/storage-classes/express-one-zone/)，以提高性能。

## 语法

```sql
CREATE TABLE [IF NOT EXISTS] [db.]table_name (
    <column_name> <data_type> [NOT NULL | NULL] [{ DEFAULT <expr> }],
    <column_name> <data_type> [NOT NULL | NULL] [{ DEFAULT <expr> }],
    ...
)
's3://<bucket>/[<path>]'
CONNECTION = (
    ENDPOINT_URL = 'https://<endpoint-URL>'
    ACCESS_KEY_ID = '<your-access-key-ID>'
    SECRET_ACCESS_KEY = '<your-secret-access-key>'
    REGION = '<region-name>'
    ENABLE_VIRTUAL_HOST_STYLE = 'true' | 'false'
)
|
CONNECTION = (
    CONNECTION_NAME = '<your-connection-name>'
);
```

连接参数：

| Parameter                 | Description                                                                                                                                                      | Required |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `s3://<bucket>/[<path>]`  | 文件位于指定的外部位置（类 S3 存储桶）                                                                                                                           | YES      |
| ENDPOINT_URL              | 以 "https://" 开头的存储桶 endpoint URL。要使用以 "http://" 开头的 URL，请在文件 `databend-query-node.toml` 的 [storage] 块中将 `allow_insecure` 设置为 `true`。 | Optional |
| ACCESS_KEY_ID             | 用于连接 AWS S3 兼容对象存储的访问密钥 ID。如果未提供，Databend 将以匿名方式访问存储桶。                                                                         | Optional |
| SECRET_ACCESS_KEY         | 用于连接 AWS S3 兼容对象存储的密钥。                                                                                                                             | Optional |
| REGION                    | AWS 区域名称。例如，us-east-1。                                                                                                                                  | Optional |
| ENABLE_VIRTUAL_HOST_STYLE | 如果您使用虚拟主机来寻址存储桶，请将其设置为 "true"。                                                                                                            | Optional |

有关 `CONNECTION_NAME` 的更多信息，请参见 [CREATE CONNECTION](../13-connection/create-connection.md)

## S3 兼容存储桶策略要求

外部位置 S3 存储桶必须通过 S3 存储桶策略授予以下权限：

**只读访问权限：**

- `s3:GetObject`：允许从存储桶读取对象。
- `s3:ListBucket`：允许列出存储桶中的对象。
- `s3:ListBucketVersions`：允许列出存储桶中的对象版本。
- `s3:GetObjectVersion`：允许检索对象的特定版本。

**可写访问权限：**

- `s3:PutObject`：允许将对象写入存储桶。
- `s3:DeleteObject`：允许删除存储桶中的对象。
- `s3:AbortMultipartUpload`：允许中止分段上传。
- `s3:DeleteObjectVersion`：允许删除对象的特定版本。
  :::

## 示例

:::info

在使用 `SHOW CREATE TABLE` 命令之前，您需要将 `hide_options_in_show_create_table` 变量设置为 `0`。

```sql
SET GLOBAL hide_options_in_show_create_table = 0;
```

:::

### 创建具有外部位置的表

创建一个表，并将数据存储在外部位置，例如 Amazon S3：

```sql
-- 创建一个名为 `mytable` 的表，并指定位置 `s3://testbucket/admin/data/` 用于数据存储
CREATE TABLE mytable (
  a INT
)
's3://testbucket/admin/data/'
CONNECTION = (
  ACCESS_KEY_ID = '<your_aws_key_id>',
  SECRET_ACCESS_KEY = '<your_aws_secret_key>',
  ENDPOINT_URL = 'https://s3.amazonaws.com'
);

-- 显示表结构
SHOW CREATE TABLE mytable;

CREATE TABLE mytable (
  a INT NULL
)
ENGINE = FUSE
COMPRESSION = 'zstd'
STORAGE_FORMAT = 'parquet'
LOCATION = 's3 | bucket=testbucket,root=/admin/data/,endpoint=https://s3.amazonaws.com';
```

### 使用连接创建表

或者，您可以创建一个连接并使用它来创建表：

```sql
-- 为 S3 凭据创建一个名为 `s3_connection` 的连接
CREATE CONNECTION s3_connection
  STORAGE_TYPE = 's3'
  SECRET_ACCESS_KEY = '<your-secret-access-key>'
  ACCESS_KEY_ID = '<your-access-key-id>';

CREATE TABLE mytable (
  a INT
)
's3://testbucket/admin/data/'
CONNECTION = (
  CONNECTION_NAME = 's3_connection'
);

-- 显示表结构
SHOW CREATE TABLE mytable;

CREATE TABLE mytable (
  a INT NULL
)
ENGINE = FUSE
COMPRESSION = 'zstd'
STORAGE_FORMAT = 'parquet'
LOCATION = 's3 | bucket=testbucket,root=/admin/data/,endpoint=https://s3.amazonaws.com';
```
