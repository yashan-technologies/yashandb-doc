## Functionality Overview

Allocates storage for parameters in SQL statements.

## Function Declaration

```c
SQLRETURN SQLBindParameter(  
      SQLHSTMT        StatementHandle,  
      SQLUSMALLINT    ParameterNumber,  
      SQLSMALLINT     InputOutputType,  
      SQLSMALLINT     ValueType,  
      SQLSMALLINT     ParameterType,  
      SQLULEN         ColumnSize,  
      SQLSMALLINT     DecimalDigits,  
      SQLPOINTER      ParameterValuePtr,  
      SQLLEN          BufferLength,  
      SQLLEN *        StrLen_or_IndPtr);
```

## Parameter Description

|Parameter Name |Description |
| ---------------------- | ------------------------------------------------ |
| StatementHandle (IN)     | Statement handle.                               |
| ParameterNumber (IN)     | Parameter number, ordered sequentially starting from 1. |
| InputOutputType (IN)     | Type of the parameter.                         |
| ValueType (IN)           | C data type of the parameter.                  |
| ParameterType (IN)       | SQL data type of the parameter.                |
| ColumnSize (IN)          | Size of the column or expression associated with the parameter. |
| DecimalDigits (IN)       | Number of decimal digits for the column or expression associated with the parameter. |
| ParameterValuePtr (IN)   | Pointer to the cache of parameter data.        |
| BufferLength (IN/OUT)    | Length of the ParameterValuePtr cache (in bytes). |
| StrLen_or_IndPtr (IN)    | Pointer to the cache for parameter length.     |

Support for InputOutputType parameter:

|InputOutputType |Description |Support Status |
| ----------------------------- | ---- | -------- |
| SQL_PARAM_INPUT               |             | Supported      |
| SQL_PARAM_OUTPUT              |             | Supported      |
| SQL_PARAM_INPUT_OUTPUT        |             | Not Supported  |
| SQL_PARAM_INPUT_OUTPUT_STREAM |             | Not Supported  |
| SQL_PARAM_OUTPUT_STREAM       |             | Not Supported  |

Support for StrLen_or_IndPtr parameter:

|StrLen_or_IndPtr |Description |Support Status |
| --------------------------------------------- | ------------------------------------- | -------- |
| Length of the parameter value stored in *ParameterValuePtr. | Ignored except for character or binary C data. | Supported      |
| SQL_NTS                                       | Parameter value is a null-terminated string. | Supported      |
| SQL_NULL_DATA                                 | Parameter value is NULL.                   | Supported      |
| SQL_DEFAULT_PARAM                             |                                            | Not Supported  |
| SQL_LEN_DATA_AT_EXEC                          | Parameter data will be sent using SQLPutData. | Supported      |
| SQL_DATA_AT_EXEC                              | Parameter data will be sent using SQLPutData. | Supported      |

> **Note**: 
> When using `:name` style placeholders in precompiled SQL and multiple placeholders have the same name, it is necessary to bind the value multiple times in position binding. Typically, the same value is bound at the position of the same named placeholder.