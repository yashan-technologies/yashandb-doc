## Functionality Overview

The yacDirectExecute function is used to execute SQL requests initiated by the client. It supports executing SQL statements that do not require bound data or SQL statements that require bound parameter data. A return value of YAC_SUCCESS indicates successful execution, while a return value of YAC_ERROR indicates execution failure.

## Function Declaration

```c
yacResult yacDirectExecute(yacHandle hStmt,
                           const yacChar* sql,
                           YacInt32 sqlLength);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------------------------- |
| hStmt (IN/OUT)   | SQL information handle.                              |
| sql (IN)         | SQL statement string.                               |
| sqlLength (IN)   | Length of the SQL statement. You can pass YAC_NULL_TERM_STR to indicate a null-terminated string. |