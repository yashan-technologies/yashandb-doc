## Functionality Introduction

The yacGetStmtAttr function is used to retrieve a certain attribute value from the SQL statement handle. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

Statement-level parameters can affect the behavior of the current SQL statement handle. Please configure accordingly using the [yacSetStmtAttr](yacSetStmtAttr) function.

## Function Declaration

```c
yacResult yacGetStmtAttr(yacHandle hStmt,
                         yacStmtAttr attr,
                         YacVoid* value,
                         YacInt32 bufLength,
                         YacInt32* stringLength);
```

## Parameter Description

|Parameter Name |Description |
| ------------------ | ---------------------------- |
| hStmt (IN/OUT)      | SQL statement handle.              |
| attr (IN)           | Attribute type.                    |
| value (OUT)         | Stores the retrieved attribute value. |
| bufLength (OUT)     | Cache length of the retrieved attribute value. |
| stringLength (OUT)  | Actual length of the retrieved attribute value. |