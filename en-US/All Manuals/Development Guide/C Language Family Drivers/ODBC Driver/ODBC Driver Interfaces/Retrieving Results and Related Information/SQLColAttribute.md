## Functionality Introduction

Describes the properties of the columns in the result set.

## Function Declaration

```c
SQLRETURN SQLColAttribute (  
      SQLHSTMT        StatementHandle,  
      SQLUSMALLINT    ColumnNumber,  
      SQLUSMALLINT    FieldIdentifier,  
      SQLPOINTER      CharacterAttributePtr,  
      SQLSMALLINT     BufferLength,  
      SQLSMALLINT *   StringLengthPtr,  
      SQLLEN *        NumericAttributePtr);
```

## Parameter Description

|Parameter Name |Description |
| --------------------------- | ------------------------------------------------------------ |
| StatementHandle (IN)          | Statement handle.                                            |
| ColumnNumber (IN)             | The record number in the IRD from which to retrieve the field value. |
| FieldIdentifier (IN)          | Descriptor handle.                                          |
| CharacterAttributePtr (OUT)   | Pointer to the cache.                                      |
| BufferLength (IN)             | If FieldIdentifier is an ODBC-defined field, and CharacterAttributePtr points to a string or binary cache, then the length of this parameter should be *CharacterAttributePtr. |
| StringLengthPtr (OUT)         | Pointer to the cache that returns the total number of bytes (excluding null-termination byte) in *CharacterAttributePtr. |
| NumericAttributePtr (OUT)     | Pointer to the integer cache.                              |

Supported status of FieldIdentifier parameter:

|FieldIdentifier |Description |Support Status |
| -------------------------- | ------------------------------------------------------------ | -------- |
| SQL_DESC_CONCISE_TYPE        | Concise type                                                 | Supported       |
| SQL_DESC_DISPLAY_SIZE        | Maximum display width; for variable-length data types (string types and binary types), it is recommended to bind cache size as this plus 1 (for the null terminator '\0') | Supported       |
| SQL_DESC_LENGTH              | Equivalent to SQL_DESC_DISPLAY_SIZE                          | Supported       |
| SQL_DESC_NAME                | Column name                                                  | Supported       |
| SQL_COLUMN_LABEL             | Equivalent to SQL_DESC_NAME (ODBC 2.0)                       | Supported       |
| SQL_DESC_NULLABLE            | Nullability                                                  | Supported       |
| SQL_DESC_OCTET_LENGTH        | Equivalent to SQL_DESC_DISPLAY_SIZE                          | Supported       |
| SQL_DESC_PRECISION           | Precision                                                    | Supported       |
| SQL_DESC_SCALE               | Scale                                                       | Supported       |
| SQL_DESC_TYPE                | Return value varies based on sqlType<br />switch (sqlType) {<br/>    case SQL_TYPE_DATE:<br/>    case SQL_TYPE_TIME:<br/>    case SQL_TYPE_TIMESTAMP:<br/>        *value = SQL_DATETIME;<br/>        break;<br/>    case SQL_INTERVAL_DAY_TO_SECOND:<br/>    case SQL_INTERVAL_YEAR_TO_MONTH:<br/>        *value = SQL_INTERVAL;<br/>        break;<br/>    default:<br/>        *value = sqlType;<br/>        break;<br/>} | Supported       |
| SQL_DESC_UNSIGNED            | Whether it is unsigned                                       | Supported       |
| SQL_DESC_AUTO_UNIQUE_VALUE   |                                                              | Not Supported   |
| SQL_DESC_BASE_COLUMN_NAME    |                                                              | Not Supported   |
| SQL_DESC_BASE_TABLE_NAME     |                                                              | Not Supported   |
| SQL_DESC_CASE_SENSITIVE      |                                                              | Not Supported   |
| SQL_DESC_CATALOG_NAME        |                                                              | Not Supported   |
| SQL_DESC_FIXED_PREC_SCALE    |                                                              | Not Supported   |
| SQL_DESC_LITERAL_PREFIX      |                                                              | Not Supported   |
| SQL_DESC_LITERAL_SUFFIX      |                                                              | Not Supported   |
| SQL_DESC_NUM_PREC_RADIX      |                                                              | Not Supported   |
| SQL_DESC_SCHEMA_NAME         |                                                              | Not Supported   |
| SQL_DESC_SEARCHABLE          |                                                              | Not Supported   |
| SQL_DESC_TABLE_NAME          |                                                              | Not Supported   |
| SQL_DESC_TYPE_NAME           |                                                              | Not Supported   |
| SQL_DESC_UNNAMED             |                                                              | Not Supported   |
| SQL_DESC_UPDATABLE           |                                                              | Not Supported   |