## Functionality Introduction

Returns information about the data types supported by the data source.

## Function Declaration

```c
SQLRETURN SQLGetTypeInfo(  
     SQLHSTMT      StatementHandle,  
     SQLSMALLINT   DataType);
```

## Parameter Description

|Parameter Name |Description |
| -------------------- | ------------------ |
| StatementHandle (IN) | The statement handle for the result set. |
| DataType (IN)       | SQL data type.                  |