## Functionality Overview

The OCITransForget function is used to remove the status of a transaction from the pending transaction table.

## Function Declaration

```c
sword OCITransForget ( OCISvcCtx     *svchp, 
                       OCIError      *errhp,
                       ub4           flags);
```

## Parameter Description

|Parameter Name |Description |
|-------------|------------------------|
| svchp (IN)     | Service context handle.                |
| errhp (IN)     | An error handle to obtain diagnostic information when an error occurs. |
| flags (IN)     | Flags for global transactions.         |

Some parameters currently only support the following values:

|Parameter |Valid Values |
|--------|------------|
| flags  | TMNOFLAGS  |
