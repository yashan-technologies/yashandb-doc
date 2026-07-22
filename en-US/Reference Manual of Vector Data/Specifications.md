## Vector Type

When defining a data column as a vector type, the syntax rules are as follows:

```ebnf
col_definition = column_name VECTOR "(" dimension, format ")".
```

|Item |Description |
| -------------- | ------------------------------------------------------------ |
| Dimension | (0-65535] |
| Format | FLOAT32, FLOAT64 |
| Storage Format | DENSE |
| DML | - Supports INSERT, DELETE, and UPDATE for vector types;<br>- Query supports ORDER BY, FETCH, LIMIT, and exact query of vector columns;<br>- Supports vector functions and vector operators. |

**Usage Constraints**

- Only vector types are supported on HEAP tables;
- CHECK, UNIQUE, and PRIMARY KEY constraints are not allowed on vector columns, but NOT NULL constraints are supported;
- Vector columns in tables are not allowed to have DEFAULT values specified;
- ALTER TABLE MODIFY is not allowed for changing the data type or dimension information of vector columns, but modifying NULL/VISIBLE attributes is supported;
- Column encryption is not allowed for vector columns;
- Vector columns are not allowed to be used as partition columns for table partitioning;
- User-defined types (UDTs) with vector types are not supported;
- Vector column computation does not support DISTINCT, COUNT, ORDER BY, GROUP BY, JOIN operations, nor set operations (except UNION ALL);
- Vector columns do not support comparison operators (>, <, =, !=).

YashanDB supports conversion between vector data and character or CLOB types, which can be performed either explicitly using conversion functions or implicitly.

- In INSERT, UPDATE, and vector operation scenarios, if the input parameter data type differs from the target parameter data type, implicit conversion is automatically performed. Implicit conversion between CHAR/VARCHAR/CLOB types and vector types is supported;
- The TO_VECTOR/VECTOR function is supported to convert VARCHAR/CLOB type data to VECTOR type. The vector dimension is determined by the input parameter, and the vector value data type defaults to FLOAT32;
- The TO_VECTOR/VECTOR function is supported to convert the dimension value and data type of the original vector data;
- The FROM_VECTOR function is supported to convert vector data to VARCHAR or CLOB type data.

The SQL statement example of creating vector table with vector column is as follows:

```sql
CREATE TABLE vector_table(
    doc_id BIGINT NOT NULL,
    chunk_id BIGINT NOT NULL,
    content VARCHAR(4000),
    embedding VECTOR(1024, FLOAT32)
) ORGANIZATION HEAP;
```

## Vector Index

|Item |Description |
| -------- | ------------------------------------------------------------ |
| Index Type | HNSW |
| Storage Method | Disk index |
| Dimension | (0-65535] |
| Metric | COSINE, EUCLIDEAN, DOT,  EUCLIDEAN_SQUARED |
| Parameters | - neighbors: [2, 100], default value 16;<br>- efConstruction: [4, 1000], default value 64;<br>- efSearch: [1, 1000], default is 40; |
| DDL | - Supports CREATE/DROP vector indexes;<br>-Support ALTER vector indexes; |
| DML | - Supports incremental DML operations such as INSERT, DELETE, and UPDATE; <br>- Supports strong transaction consistency for vector indexes; |

**Usage Constraints**

- Creating multiple vector indexes on a single table is not allowed;
- Creating function indexes that include vector columns is not allowed;
- Specifying the ONLINE option when creating or rebuilding an HNSW vector index is not allowed;
- Creating an HNSW vector index on global temporary tables is not allowed.
