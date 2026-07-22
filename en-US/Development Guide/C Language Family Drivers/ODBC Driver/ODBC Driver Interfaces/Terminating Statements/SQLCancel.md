## Functionality Introduction

Cancel the execution of the current statement. If the current statement has already been completed or is not in an executing state, it returns SQL_SUCCESS.

## Function Declaration

```c
SQLRETURN SQLCancel(  
     SQLHSTMT     StatementHandle);
```

## Parameter Explanation

|Parameter Name |Description |
| -------------------- | ---------- |
| StatementHandle (IN) | Statement handle. |