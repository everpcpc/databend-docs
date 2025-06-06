---
title: Querying Avro Files in Stage
sidebar_label: Avro
---

## Query Avro Files in Stage

Syntax:
```sql
SELECT [<alias>.]$1:<column> [, $1:<column> ...]
FROM {@<stage_name>[/<path>] [<table_alias>] | '<uri>' [<table_alias>]}
[(
  [<connection_parameters>],
  [ PATTERN => '<regex_pattern>'],
  [ FILE_FORMAT => 'AVRO'],
  [ FILES => ( '<file_name>' [ , '<file_name>' ] [ , ... ] ) ]
)]
```

:::info Tips
**Query Return Content Explanation:**

* **Return Format**: Each row as a single variant object (referenced as `$1`)
* **Access Method**: Use path expressions `$1:column_name`
* **Example**: `SELECT $1:id, $1:name FROM @stage_name`
* **Key Features**:
  * Must use path notation to access specific fields
  * Type casting required for type-specific operations (e.g., `CAST($1:id AS INT)`)
  * Avro schema is mapped to variant structure
  * Whole row is represented as a single variant object
:::

## Avro Querying Features Overview

Databend provides comprehensive support for querying Avro files directly from stages. This allows for flexible data exploration and transformation without needing to load the data into a table first.

*   **Variant Representation**: Each row in an Avro file is treated as a variant, referenced by `$1`. This allows for flexible access to nested structures within the Avro data.
*   **Type Mapping**: Each Avro type is mapped to a corresponding variant type in Databend.
*   **Metadata Access**: You can access metadata columns like `METADATA$FILENAME` and `METADATA$FILE_ROW_NUMBER` for additional context about the source file and row.

## Tutorial

This tutorial demonstrates how to query Avro files stored in a stage.

### Step 1. Prepare an Avro File

Consider an Avro file with the following schema named `user`:

```json
{
  "type": "record",
  "name": "user",
  "fields": [
    {
      "name": "id",
      "type": "long"
    },
    {
      "name": "name",
      "type": "string"
    }
  ]
}
```

### Step 2. Create an External Stage

Create an external stage with your own S3 bucket and credentials where your Avro files are stored.

```sql
CREATE STAGE avro_query_stage
URL = 's3://load/avro/'
CONNECTION = (
    ACCESS_KEY_ID = '<your-access-key-id>'
    SECRET_ACCESS_KEY = '<your-secret-access-key>'
);
```

### Step 3. Query Avro Files

#### Basic Query

Query Avro files directly from a stage:

```sql
SELECT
    CAST($1:id AS INT) AS id,
    $1:name AS name
FROM @avro_query_stage
(
    FILE_FORMAT => 'AVRO',
    PATTERN => '.*[.]avro'
);
```

### Query with Metadata

Query Avro files directly from a stage, including metadata columns like `METADATA$FILENAME` and `METADATA$FILE_ROW_NUMBER`:

```sql
SELECT
    METADATA$FILENAME,
    METADATA$FILE_ROW_NUMBER,
    CAST($1:id AS INT) AS id,
    $1:name AS name
FROM @avro_query_stage
(
    FILE_FORMAT => 'AVRO',
    PATTERN => '.*[.]avro'
);
```

## Type Mapping to Variant

Variants in Databend are stored as JSONB. While most Avro types map straightforwardly, some special considerations apply:

*   **Time Types**: `TimeMillis` and `TimeMicros` are mapped to `INT64` as JSONB does not have a native Time type. Users should be aware of the original type when processing these values.
*   **Decimal Types**: Decimals are loaded as `DECIMAL128` or `DECIMAL256`. An error may occur if the precision exceeds the supported limits.
*   **Enum Types**: Avro `ENUM` types are mapped to `STRING` values in Databend.
