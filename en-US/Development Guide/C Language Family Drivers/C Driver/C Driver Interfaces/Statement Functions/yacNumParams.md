## Functionality Overview

The yacNumParams function is used to obtain the number of parameters in a statement. It returns YAC_SUCCESS to indicate success, and YAC_ERROR to indicate failure.

## Function Declaration

```c
YacResult yacNumParams(YacHandle hStmt,
                       YacUint16* count);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ---------------------- |
| hStmt (IN/OUT)  | SQL information handle.               |
| count (OUT)     | Stores the number of parameters in the statement. |