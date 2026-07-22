## 功能简介

返回单个描述符字段的值。

## 函数声明

```c
SQLRETURN SQLGetDescField(
     SQLHDESC        DescriptorHandle,
     SQLSMALLINT     RecNumber,
     SQLSMALLINT     FieldIdentifier,
     SQLPOINTER      ValuePtr,
     SQLINTEGER      BufferLength,
     SQLINTEGER *    StringLengthPtr);
```

## 参数说明

|  参数名| 说明|
| --------------------- | ------------------------------------------------------------ |
| DescriptorHandle (IN) | 描述符句柄。                                                 |
| RecNumber (IN)        | 指示应用程序从中查找信息的描述符记录。                       |
| FieldIdentifier (IN)  | 指示要返回其值的描述符的字段。                               |
| ValuePtr (OUT)        | 指向要在其中返回描述符信息的缓冲区的指针。                   |
| BufferLength (IN)     | 如果 FieldIdentifier 是 ODBC 定义的字段，并且 ValuePtr 指向字符串或二进制缓冲区，则此参数应为 *ValuePtr 的长度。 |
| StringLengthPtr (OUT) | 指向缓冲区的指针，该缓冲区将返回总字节数所需的字节数。       |

## FieldIdentifier参数支持情况

不在列表中的参数不支持，同时已做拦截处理。

### ARD

|  字段属性| FieldIdentifier| 支持情况| 说明|
| -------- | ------------------------------- | -------- | ------------------------------------------------------------ |
| header   | SQL_DESC_ALLOC_TYPE             | 支持     | 目前都为SQL_DESC_ALLOC_AUTO                                  |
| header   | SQL_DESC_ARRAY_SIZE             | 支持     | 等价于SQLSetStmtAttr的SQL_ATTR_ROW_ARRAY_SIZE                |
| header   | SQL_DESC_BIND_TYPE              | 支持     | 仅支持SQL_BIND_BY_COLUMN                                     |
| header   | SQL_DESC_COUNT                  | 支持     |                                                              |
| record   | SQL_DESC_CONCISE_TYPE           | 支持     |                                                              |
| record   | SQL_DESC_DATA_PTR               | 支持     |                                                              |
| record   | SQL_DESC_DATETIME_INTERVAL_CODE | 支持     | 详见下文返回值说明。                               |
| record   | SQL_DESC_INDICATOR_PTR          | 支持     |                                                              |
| record   | SQL_DESC_NUM_PREC_RADIX         | 支持     | 一直返回10                                                   |
| record   | SQL_DESC_OCTET_LENGTH           | 支持     |                                                              |
| record   | SQL_DESC_PRECISION              | 支持     |                                                              |
| record   | SQL_DESC_SCALE                  | 支持     |                                                              |
| record   | SQL_DESC_TYPE                   | 支持     | 详见下文返回值说明。                               |

### APD

|  字段属性| FieldIdentifier| 支持情况| 值说明|
| -------- | ------------------------------- | -------- | ------------------------------------------------------------ |
| header   | SQL_DESC_ALLOC_TYPE             | 支持     | 目前都为SQL_DESC_ALLOC_AUTO                                  |
| header   | SQL_DESC_ARRAY_SIZE             | 支持     | 等价于SQLSetStmtAttr的SQL_ATTR_PARAMSET_SIZE                 |
| header   | SQL_DESC_BIND_TYPE              | 支持     | 仅支持SQL_BIND_BY_COLUMN                                     |
| header   | SQL_DESC_COUNT                  | 支持     |                                                              |
| record   | SQL_DESC_CONCISE_TYPE           | 支持     |                                                              |
| record   | SQL_DESC_DATA_PTR               | 支持     |                                                              |
| record   | SQL_DESC_DATETIME_INTERVAL_CODE | 支持     | 详见下文返回值说明。                               |
| record   | SQL_DESC_INDICATOR_PTR          | 支持     |                                                              |
| record   | SQL_DESC_NUM_PREC_RADIX         | 支持     | 一直返回10                                                   |
| record   | SQL_DESC_OCTET_LENGTH           | 支持     |                                                              |
| record   | SQL_DESC_PRECISION              | 支持     |                                                              |
| record   | SQL_DESC_SCALE                  | 支持     |                                                              |
| record   | SQL_DESC_TYPE                   | 支持     | 详见下文返回值说明。                               |

### IRD

|  字段属性| FieldIdentifier| 支持情况| 值说明|
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------------------------- |
| header   | SQL_DESC_ALLOC_TYPE                                          | 支持                                                         | 目前都为SQL_DESC_ALLOC_AUTO                     |
| header   | SQL_DESC_COUNT                                               | 支持                                                         |                                                 |
| header   | SQL_DESC_ROWS_PROCESSED_PTR                                  | 支持                                                         | 等价于SQLSetStmtAttr的SQL_ATTR_ROWS_FETCHED_PTR |
| record   | SQL_DESC_AUTO_UNIQUE_VALUE <br />SQL_DESC_BASE_COLUMN_NAME <br />SQL_DESC_BASE_TABLE_NAME <br />SQL_DESC_CASE_SENSITIVE <br />SQL_DESC_CATALOG_NAME <br />SQL_DESC_CONCISE_TYPE <br />SQL_DESC_DISPLAY_SIZE <br />SQL_DESC_FIXED_PREC_SCALE <br />SQL_DESC_LABEL <br />SQL_DESC_LENGTH <br />SQL_DESC_LITERAL_PREFIX <br />SQL_DESC_LITERAL_SUFFIX <br />SQL_DESC_LOCAL_TYPE_NAME <br />SQL_DESC_NAME <br />SQL_DESC_NULLABLE <br />SQL_DESC_NUM_PREC_RADIX <br />SQL_DESC_OCTET_LENGTH <br />SQL_DESC_PRECISION <br />SQL_DESC_SCALE <br />SQL_DESC_SCHEMA_NAME <br />SQL_DESC_SEARCHABLE <br />SQL_DESC_TABLE_NAME <br />SQL_DESC_TYPE <br />SQL_DESC_TYPE_NAME <br />SQL_DESC_UNNAMED <br />SQL_DESC_UNSIGNED <br />SQL_DESC_UPDATABLE | 参见[SQLColAttribute](../检索结果及其相关信息/SQLColAttribute)的字段支持情况 | 等价于SQLColAttribute中的对应字段属性           |

### IPD

|  字段属性| FieldIdentifier| 支持情况| 值说明|
| -------- | ----------------------- | -------- | ------------------------------------------------------------ |
| header   | SQL_DESC_ALLOC_TYPE     | 支持     | 目前都为SQL_DESC_ALLOC_AUTO                                  |
| header   | SQL_DESC_COUNT          | 支持     |                                                              |
| record   | SQL_DESC_CONCISE_TYPE   | 支持     | 当该字段未设置时，返回SLQ_UNKNOWN_TYPE，当该字段已设置，返回对应已设置的sqlType |
| record   | SQL_DESC_PARAMETER_TYPE | 支持     |                                                              |
| record   | SQL_DESC_PRECISION      | 不支持   |                                                              |
| record   | SQL_DESC_SCALE          | 不支持   |                                                              |
| record   | SQL_DESC_TYPE           | 支持     | 详见下文返回值说明。                               |

## 返回值说明

以下字段的返回值根据类型不同，返回对应值：

### SQL_DESC_DATETIME_INTERVAL_CODE (ARD/APD)

```c
switch (bindCType) {
    case SQL_C_TYPE_DATE:
        *value = SQL_CODE_DATE;
        break;
    case SQL_C_TYPE_TIME:
        *value = SQL_CODE_TIME;
        break;
    case SQL_C_TYPE_TIMESTAMP:
        *value = SQL_CODE_TIMESTAMP;
        break;
    case SQL_C_INTERVAL_DAY_TO_SECOND:
        *value = SQL_CODE_DAY_TO_SECOND;
        break;
    case SQL_C_INTERVAL_YEAR_TO_MONTH:
        *value = SQL_CODE_YEAR_TO_MONTH;
        break;
    default:
        *value = 0;
        break;
}
```

### SQL_DESC_TYPE (ARD/APD)

```c
switch (bindCType) {
    case SQL_C_TYPE_DATE:
    case SQL_C_TYPE_TIME:
    case SQL_C_TYPE_TIMESTAMP:
        *value = SQL_DATETIME;
        break;
    case SQL_C_INTERVAL_DAY_TO_SECOND:
    case SQL_C_INTERVAL_YEAR_TO_MONTH:
        *value = SQL_INTERVAL;
        break;
    default:
        *value = bindCType;
        break;
}
```

### SQL_DESC_TYPE (IPD)

```c
switch (sqlType) {
    case SQL_TYPE_DATE:
    case SQL_TYPE_TIME:
    case SQL_TYPE_TIMESTAMP:
        *value = SQL_DATETIME;
        break;
    case SQL_INTERVAL_DAY_TO_SECOND:
    case SQL_INTERVAL_YEAR_TO_MONTH:
        *value = SQL_INTERVAL;
        break;
    default:
        *value = sqlType;
        break;
}
```