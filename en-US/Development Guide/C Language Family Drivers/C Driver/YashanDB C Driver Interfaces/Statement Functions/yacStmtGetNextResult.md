## Functionality Introduction

The `yacStmtGetNextResult` function is used to retrieve implicit result sets. It returns `YAC_SUCCESS` to indicate successful retrieval and `YAC_ERROR` to indicate failure.

## Function Declaration

```c
YacResult yacStmtGetNextResult(YacHandle  hStmt,
                               YacHandle* cursor,
                               YacUint32* rtType);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ---------------------------------------------------- |
| hStmt (IN/OUT)  | SQL information handle.                            |
| cursor (OUT)    | Cursor returned to the client by the server via `dbms_sql.return_result`. |
| rtType (OUT)    | Implicit result set type, currently fixed to return `YAC_RESULT_TYPE_SELECT`. |