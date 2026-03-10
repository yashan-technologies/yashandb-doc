## Functionality Overview

Returns the number of columns in the result set.

## Function Declaration

```c
SQLRETURN SQLNumResultCols(  
     SQLHSTMT        StatementHandle,  
     SQLSMALLINT *   ColumnCountPtr);
```

## Parameter Description

|Parameter Name |Description |
| -------------------- | ------------------------------------------------ |
| StatementHandle (IN) | Statement handle.                               |
| ColumnCountPtr (OUT) | Pointer to a cache that will return the number of columns in the result set. |