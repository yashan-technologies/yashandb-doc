## Functionality Overview

Allocate storage for result columns and specify data types. Binding must be done column by column.

Currently, output data cache does not support truncating variable-length types. When retrieving result set data, any variable-length type encountering insufficient output data cache will raise an error.

## Function Declaration

```c
SQLRETURN SQLBindCol(  
      SQLHSTMT       StatementHandle,  
      SQLUSMALLINT   ColumnNumber,  
      SQLSMALLINT    TargetType,  
      SQLPOINTER     TargetValuePtr,  
      SQLLEN         BufferLength,  
      SQLLEN *       StrLen_or_IndPtr);
```

## Parameter Description

|Parameter Name |Description |
| ------------------------- | --------------------------------------------- |
| StatementHandle (IN)         | Statement handle.                             |
| ColumnNumber (IN)            | The number of the result set column to bind. |
| TargetType (IN)              | Identifier of the C data type for *TargetValuePtr cache. |
| TargetValuePtr (IN/OUT)      | Pointer to the data cache to bind to the column. |
| BufferLength (IN)            | Length (in bytes) of the *TargetValuePtr cache. |
| StrLen_or_IndPtr (IN/OUT)   | Pointer to the length/indicator cache to bind to the column. |

Support for StrLen_or_IndPtr parameter:

The following are returnable results:

|StrLen_or_IndPtr |Description |Support Status |
| -------------------------------------------- | ---------------------- | -------- |
| Length of the result stored in **ParameterValuePtr**. | Length of the data that can be returned. | Supported       |
| SQL_NULL_DATA                                 | Result is null                       | Supported       |