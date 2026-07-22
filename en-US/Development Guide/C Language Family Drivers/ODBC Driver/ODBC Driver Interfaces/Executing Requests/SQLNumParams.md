## Functionality Overview

Returns the number of parameters in the statement.

## Function Declaration

```c
SQLRETURN SQLNumParams(  
     SQLHSTMT        StatementHandle,  
     SQLSMALLINT *   ParameterCountPtr);
```

## Parameter Description

|Parameter Name |Description |
| ----------------------- | ---------------------------------------------- |
| StatementHandle (IN)    | The statement handle.                          |
| ParameterCountPtr (OUT) | A pointer to the cache where the number of parameters in the statement will be returned. |