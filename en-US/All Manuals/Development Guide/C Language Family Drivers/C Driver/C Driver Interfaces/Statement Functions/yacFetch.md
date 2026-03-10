## Functionality Introduction

The yacFetch function is used for the client to initiate a request to retrieve a result set. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

## Function Declaration

```c
yacResult yacFetch(yacHandle hStmt,
                   yacUint32* rows);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ----------------------------------------------- |
| hStmt (IN/OUT)  | SQL information handle.                         |
| rows (IN/OUT)   | Pointer that holds the actual number of rows retrieved by this fetch action. |