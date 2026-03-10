## Functionality Introduction

The yacPrepare function is used to initiate a precompiled SQL request from the client. It returns YAC_SUCCESS to indicate that the precompilation was successful, and returns YAC_ERROR to indicate that the precompilation failed.

## Function Declaration

```c
yacResult yacPrepare(yacHandle hStmt,
                     const yacChar* sql,
                     YacInt32 sqlLength);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------------------------- |
| hStmt (IN/OUT)   | SQL information handle.                           |
| sql (IN)         | SQL statement string.                             |
| sqlLength (IN)   | Length of the SQL statement, can pass YAC_NULL_TERM_STR to indicate it ends with \0. |