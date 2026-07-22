## Functionality Overview

Returns the value of a single descriptor field.

## Function Declaration

```c
SQLRETURN SQLGetDescField(
     SQLHDESC        DescriptorHandle,
     SQLSMALLINT     RecNumber,
     SQLSMALLINT     FieldIdentifier,
     SQLPOINTER      ValuePtr,
     SQLINTEGER      BufferLength,
     SQLINTEGER *    StringLengthPtr);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------------------------------------ |
| DescriptorHandle (IN)  | Descriptor handle.                                   |
| RecNumber (IN)         | Indicates the descriptor record from which the application retrieves information. |
| FieldIdentifier (IN)   | Indicates the field of the descriptor whose value is to be returned. |
| ValuePtr (OUT)         | Pointer to the cache where descriptor information is returned. |
| BufferLength (IN)      | If FieldIdentifier is an ODBC-defined field and ValuePtr points to a string or binary cache, this parameter should be the length of *ValuePtr. |
| StringLengthPtr (OUT)  | Pointer to a cache that returns the number of bytes required for the total byte count. |

## FieldIdentifier Parameter Support

Parameters not listed are not supported and have been intercepted.

### ARD

|Field Attribute |FieldIdentifier |Support Status |Description |
| -------- | ------------------------------- | -------- | ------------------------------------------------------------ |
| header          | SQL_DESC_ALLOC_TYPE             | Supported      | Currently all are SQL_DESC_ALLOC_AUTO                  |
| header          | SQL_DESC_ARRAY_SIZE             | Supported      | Equivalent to SQLSetStmtAttr's SQL_ATTR_ROW_ARRAY_SIZE  |
| header          | SQL_DESC_BIND_TYPE              | Supported      | Only supports SQL_BIND_BY_COLUMN                        |
| header          | SQL_DESC_COUNT                  | Supported      |                                                       |
| record          | SQL_DESC_CONCISE_TYPE           | Supported      |                                                       |
| record          | SQL_DESC_DATA_PTR               | Supported      |                                                       |
| record          | SQL_DESC_DATETIME_INTERVAL_CODE | Supported      | See Return Value Description below. |
| record          | SQL_DESC_INDICATOR_PTR          | Supported      |                                                       |
| record          | SQL_DESC_NUM_PREC_RADIX         | Supported      | Always returns 10                                     |
| record          | SQL_DESC_OCTET_LENGTH           | Supported      |                                                       |
| record          | SQL_DESC_PRECISION              | Supported      |                                                       |
| record          | SQL_DESC_SCALE                  | Supported      |                                                       |
| record          | SQL_DESC_TYPE                   | Supported      | See Return Value Description below. |

### APD

|Field Attribute |FieldIdentifier |Support Status |Value Description |
| -------- | ------------------------------- | -------- | ------------------------------------------------------------ |
| header          | SQL_DESC_ALLOC_TYPE             | Supported      | Currently all are SQL_DESC_ALLOC_AUTO                  |
| header          | SQL_DESC_ARRAY_SIZE             | Supported      | Equivalent to SQLSetStmtAttr's SQL_ATTR_PARAMSET_SIZE |
| header          | SQL_DESC_BIND_TYPE              | Supported      | Only supports SQL_BIND_BY_COLUMN                        |
| header          | SQL_DESC_COUNT                  | Supported      |                                                       |
| record          | SQL_DESC_CONCISE_TYPE           | Supported      |                                                       |
| record          | SQL_DESC_DATA_PTR               | Supported      |                                                       |
| record          | SQL_DESC_DATETIME_INTERVAL_CODE | Supported      | See Return Value Description below. |
| record          | SQL_DESC_INDICATOR_PTR          | Supported      |                                                       |
| record          | SQL_DESC_NUM_PREC_RADIX         | Supported      | Always returns 10                                     |
| record          | SQL_DESC_OCTET_LENGTH           | Supported      |                                                       |
| record          | SQL_DESC_PRECISION              | Supported      |                                                       |
| record          | SQL_DESC_SCALE                  | Supported      |                                                       |
| record          | SQL_DESC_TYPE                   | Supported      | See Return Value Description below. |

### IRD

|Field Attribute |FieldIdentifier |Support Status |Value Description |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------------------------- |
| header          | SQL_DESC_ALLOC_TYPE                                          | Supported                                                   | Currently all are SQL_DESC_ALLOC_AUTO                  |
| header          | SQL_DESC_COUNT                                               | Supported                                                   |                                                       |
| header          | SQL_DESC_ROWS_PROCESSED_PTR                                  | Supported                                                   | Equivalent to SQLSetStmtAttr's SQL_ATTR_ROWS_FETCHED_PTR |
| record          | SQL_DESC_AUTO_UNIQUE_VALUE <br />SQL_DESC_BASE_COLUMN_NAME <br />SQL_DESC_BASE_TABLE_NAME <br />SQL_DESC_CASE_SENSITIVE <br />SQL_DESC_CATALOG_NAME <br />SQL_DESC_CONCISE_TYPE <br />SQL_DESC_DISPLAY_SIZE <br />SQL_DESC_FIXED_PREC_SCALE <br />SQL_DESC_LABEL <br />SQL_DESC_LENGTH <br />SQL_DESC_LITERAL_PREFIX <br />SQL_DESC_LITERAL_SUFFIX <br />SQL_DESC_LOCAL_TYPE_NAME <br />SQL_DESC_NAME <br />SQL_DESC_NULLABLE <br />SQL_DESC_NUM_PREC_RADIX <br />SQL_DESC_OCTET_LENGTH <br />SQL_DESC_PRECISION <br />SQL_DESC_SCALE <br />SQL_DESC_SCHEMA_NAME <br />SQL_DESC_SEARCHABLE <br />SQL_DESC_TABLE_NAME <br />SQL_DESC_TYPE <br />SQL_DESC_TYPE_NAME <br />SQL_DESC_UNNAMED <br />SQL_DESC_UNSIGNED <br />SQL_DESC_UPDATABLE | Refer to [SQLColAttribute](../Retrieving Results and Related Information/SQLColAttribute) for field support status | Equivalent to corresponding field attributes in SQLColAttribute |

### IPD

|Field Attribute |FieldIdentifier |Support Status |Value Description |
| -------- | ----------------------- | -------- | ------------------------------------------------------------ |
| header          | SQL_DESC_ALLOC_TYPE     | Supported      | Currently all are SQL_DESC_ALLOC_AUTO                  |
| header          | SQL_DESC_COUNT          | Supported      |                                                       |
| record          | SQL_DESC_CONCISE_TYPE   | Supported      | Returns SQL_UNKNOWN_TYPE if the field is not set, returns the corresponding sqlType if the field is set |
| record          | SQL_DESC_PARAMETER_TYPE | Supported      |                                                       |
| record          | SQL_DESC_PRECISION      | Not Supported   |                                                       |
| record          | SQL_DESC_SCALE          | Not Supported   |                                                       |
| record          | SQL_DESC_TYPE           | Supported      | See Return Value Description below. |

## Return Value Description

The return values of the following fields vary based on the type:

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