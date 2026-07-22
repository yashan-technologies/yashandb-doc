## Functionality Introduction

The yacNumResultCols function is used to obtain the number of columns in the result set of a statement. It returns YAC_SUCCESS to indicate successful retrieval, and YAC_ERROR to indicate failure.

## Function Declaration

```c
yacResult yacNumResultCols(yacHandle hStmt,
                           yacInt16* count);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------- |
| hStmt (IN/OUT)   | SQL information handle.              |
| count (OUT)      | Stores the number of columns retrieved. |