## Functionality Overview

Set a single descriptor field.

When calling this function to set a descriptor field, the application must follow these steps in order:

1. The application must first set the SQL_DESC_TYPE, SQL_DESC_CONCISE_TYPE, or SQL_DESC_DATETIME_INTERVAL_CODE field.
2. The application sets the properties of the data type; the driver will set the data type attribute field to the corresponding default value for the data type. If the application explicitly sets the data type attributes, the driver will still overwrite them with the default attributes.
   Automatic default type attribute fields ensure that after the application specifies the data type, the descriptor is always available.
3. The application sets SQL_DESC_DATA_PTR. At this time, a consistency check will prompt for the descriptor fields; if the application changes the data type or attributes after setting the SQL_DESC_DATA_PTR field, the driver will set SQL_DESC_DATA_PTR to a null pointer, unbinding the record.

If the function is called to set any field other than SQL_DESC_COUNT or the deferred fields SQL_DESC_DATA_PTR, SQL_DESC_OCTET_LENGTH_PTR, or SQL_DESC_INDICATOR_PTR, the record will become unbound. Setting record fields other than SQL_DESC_DATA_PTR or SQL_DESC_INDICATOR_PTR will result in the record becoming unbound, and SQL_DESC_DATA_PTR will be set to NULL.

## Function Declaration

```c
SQLRETURN SQLSetDescField(
     SQLHDESC      DescriptorHandle,
     SQLSMALLINT   RecNumber,
     SQLSMALLINT   FieldIdentifier,
     SQLPOINTER    ValuePtr,
     SQLINTEGER    BufferLength);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------------------------------------ |
| DescriptorHandle (IN)  | Descriptor handle.                                        |
| RecNumber (IN)         | Indicates the descriptor record containing the fields to set. |
| FieldIdentifier (IN)   | Indicates the descriptor field whose value is to be set.  |
| ValuePtr (OUT)         | Pointer to a cache containing descriptor information or integer value. |
| BufferLength (IN)      | If FieldIdentifier is an ODBC-defined field, and ValuePtr points to a string or binary cache, this parameter should be the length of *ValuePtr. |

## FieldIdentifier Parameter Support

Parameters not listed are not supported and are intercepted.

### ARD

|Field Attribute |FieldIdentifier |Support Status |Value Description |
| -------- | ------------------------------- | -------- | ------------------------------------------------------------ |
| header                 | SQL_DESC_ARRAY_SIZE                 | Supported      | Equivalent to SQLSetStmtAttr's SQL_ATTR_ROW_ARRAY_SIZE |
| header                 | SQL_DESC_BIND_TYPE                  | Supported      | Only supports SQL_BIND_BY_COLUMN                        |
| record                 | SQL_DESC_CONCISE_TYPE               | Supported      |                                                       |
| record                 | SQL_DESC_DATA_PTR                   | Supported      |                                                       |
| record                 | SQL_DESC_DATETIME_INTERVAL_CODE     | Supported      | See Return Value Description below. |
| record                 | SQL_DESC_INDICATOR_PTR              | Supported      |                                                       |
| record                 | SQL_DESC_OCTET_LENGTH               | Supported      |                                                       |
| record                 | SQL_DESC_PRECISION                  | Supported      |                                                       |
| record                 | SQL_DESC_SCALE                      | Supported      |                                                       |
| record                 | SQL_DESC_TYPE                       | Supported      | Equivalent to SQL_DESC_CONCISE_TYPE                    |

### APD

|Field Attribute |FieldIdentifier |Support Status |Value Description |
| -------- | ------------------------------- | -------- | ------------------------------------------------------------ |
| header                 | SQL_DESC_ARRAY_SIZE                 | Supported      | Equivalent to SQLSetStmtAttr's SQL_ATTR_PARAMSET_SIZE  |
| header                 | SQL_DESC_BIND_TYPE                  | Supported      | Only supports SQL_BIND_BY_COLUMN                        |
| record                 | SQL_DESC_CONCISE_TYPE               | Supported      |                                                       |
| record                 | SQL_DESC_DATA_PTR                   | Supported      |                                                       |
| record                 | SQL_DESC_DATETIME_INTERVAL_CODE     | Supported      | See Return Value Description below. |
| record                 | SQL_DESC_INDICATOR_PTR              | Supported      |                                                       |
| record                 | SQL_DESC_OCTET_LENGTH               | Supported      |                                                       |
| record                 | SQL_DESC_PRECISION                  | Supported      |                                                       |
| record                 | SQL_DESC_SCALE                      | Supported      |                                                       |
| record                 | SQL_DESC_TYPE                       | Supported      | Equivalent to SQL_DESC_CONCISE_TYPE                    |

### IRD

|Field Attribute |FieldIdentifier |Support Status |Value Description |
| -------- | --------------------------- | -------- | ----------------------------------------------- |
| header                 | SQL_DESC_ROWS_PROCESSED_PTR      | Supported      | Equivalent to SQLSetStmtAttr's SQL_ATTR_ROWS_FETCHED_PTR |

### IPD

|Field Attribute |FieldIdentifier |Support Status |Value Description |
| -------- | ----------------------- | -------- | ------------------------------------------------------------ |
| record                 | SQL_DESC_DATA_PTR                 | Not Supported   | Not used to set this field; only triggers consistency check |
| record                 | SQL_DESC_PARAMETER_TYPE           | Supported      | Only supports SQL_PARAM_INPUT and SQL_PARAM_OUTPUT<br />Default value: SQL_PARAM_INPUT |

## Return Value Description
### SQL_DESC_DATETIME_INTERVAL_CODE (ARD/APD)

The value to be set is determined based on C Type:

```c
switch (*bindCType) {
    case SQL_DATETIME:
    case SQL_INTERVAL:
    case SQL_C_TYPE_DATE:
    case SQL_C_TYPE_TIME:
    case SQL_C_TYPE_TIMESTAMP:
    case SQL_C_INTERVAL_DAY_TO_SECOND:
    case SQL_C_INTERVAL_YEAR_TO_MONTH:
        switch (value) {
            case SQL_CODE_DATE:
                *bindCType = SQL_C_TYPE_DATE;
            case SQL_CODE_TIME:
                *bindCType = SQL_C_TYPE_TIME;
            case SQL_CODE_TIMESTAMP:
                *bindCType = SQL_C_TYPE_TIMESTAMP;
            case SQL_CODE_DAY_TO_SECOND:
                *bindCType = SQL_C_INTERVAL_DAY_TO_SECOND;
            case SQL_CODE_YEAR_TO_MONTH:
                *bindCType = SQL_C_INTERVAL_YEAR_TO_MONTH;
            default:
                break;
        }
    default:
        break;
}
```