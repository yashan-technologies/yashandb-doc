## Functionality Overview

Returns part or all of the data from a result set in a single column.

Currently supports four levels: SQL_GD_ANY_COLUMN, SQL_GD_ANY_ORDER, SQL_GD_BOUND, SQL_GD_BLOCK. SQL_GD_OUTPUT_PARAMS is not supported.

## Function Declaration

```c
SQLRETURN SQLGetData(  
      SQLHSTMT       StatementHandle,  
      SQLUSMALLINT   Col_or_Param_Num,  
      SQLSMALLINT    TargetType,  
      SQLPOINTER     TargetValuePtr,  
      SQLLEN         BufferLength,  
      SQLLEN *       StrLen_or_IndPtr);
```

## Parameter Description

|Parameter Name |Description |
| ---------------------- | ------------------------------------------------------------ |
| StatementHandle (IN)      | The statement handle.                                     |
| Col_or_Param_Num (IN)    | For retrieving column data, it is the number of the column for which data is to be returned. |
| TargetType (IN)           | The data type identifier for the C data type of *TargetValuePtr cache, SQL_ARD_TYPE is not supported. |
| TargetValuePtr (OUT)      | A pointer to the cache where the data is returned.        |
| BufferLength (IN)         | The length (in bytes) of the *TargetValuePtr cache.      |
| StrLen_or_IndPtr (OUT)    | A pointer to the cache where the length or indicator value is returned. |