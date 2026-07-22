## Functionality Overview

Returns the supported driver function.

This function differs from [SQLGetTypeInfo](SQLGetTypeInfo) in that SQLGetTypeInfo returns results in a ResultSet manner, while SQLGetFunctions operates as follows (taking the parameter support for the SQLTABLES, SQLCOLUMNS, and SQLSTATISTICS functions in an ODBC3 driver as an example):

```c
RETCODE retcodeTables, retcodeColumns, retcodeStatistics 
SQLUSMALLINT fExists[SQL_API_ODBC3_ALL_FUNCTIONS_SIZE]; 
   
retcode = SQLGetFunctions(hdbc, SQL_API_ODBC3_ALL_FUNCTIONS, fExists); 
if (reccode == SQL_SUCCESS &&  
   SQL_FUNC_EXISTS(fExists, SQL_API_SQLTABLES) == SQL_TRUE && 
   SQL_FUNC_EXISTS(fExists, SQL_API_SQLCOLUMNS) == SQL_TRUE && 
   SQL_FUNC_EXISTS(fExists, SQL_API_SQLSTATISTICS) == SQL_TRUE)  
{ 
   // Continue with application 
}
```

## Function Declaration

```c
SQLRETURN SQLGetFunctions(  
     SQLHDBC           ConnectionHandle,  
     SQLUSMALLINT      FunctionId,  
     SQLUSMALLINT *    SupportedPtr);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------------------------------------ |
| ConnectionHandle (IN)   | Connection handle.                                         |
| FunctionId (IN)        | Defines the #define value of the ODBC function; SQL_API_ODBC3_ALL_FUNCTIONS or SQL_API_ALL_FUNCTIONS. |
| SupportedPtr (OUT)     | If FunctionId identifies a single ODBC function, SupportedPtr points to a SQLUSMALLINT value; if the driver supports the specified function, it is SQL_TRUE; if not, it is SQL_FALSE.<br>If FunctionId is SQL_API_ODBC3_ALL_FUNCTIONS, SupportedPtr points to an SQLSMALLINT array of size SQL_API_ODBC3_ALL_FUNCTIONS_SIZE.<br>If FunctionId is SQL_API_ALL_FUNCTIONS, SupportedPtr points to an SQLUSMALLINT array containing 100 elements. |

> **Note**: 
>
> SQL_API_ODBC3_ALL_FUNCTIONS uses the SQL_FUNC_EXISTS macro to check function support.
>
> SQL_API_ALL_FUNCTIONS uses array position access to check function support.