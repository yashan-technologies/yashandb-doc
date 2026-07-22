## Functionality Overview

Returns part or all of the data from a result set in a single column.

Currently supports the following four levels of data retrieval. SQL_GD_OUTPUT_PARAMS is not supported:

- **SQL_GD_ANY_COLUMN**: Allows SQLGetData to be used on unbound columns even if the column order is after a bound column.
- **SQL_GD_ANY_ORDER**: Returns unbound column data in any order without strictly following the column order when calling SQLGetData.
- **SQL_GD_BOUND**: Allows SQLGetData to retrieve data from a column even if it is already bound.
- **SQL_GD_BLOCK**: In block row mode, SQLGetData can be used on unbound columns even if the fetch operation has not yet been executed.

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