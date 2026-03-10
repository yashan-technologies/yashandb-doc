## Functionality Overview

The OCITransDetach function is used to set a transaction to an inactive state.

## Function Declaration

```c
sword OCITransDetach ( OCISvcCtx    *srvcp,
                       OCIError     *errhp,
                       ub4          flags);
```

## Parameter Description

|Parameter Name |Description |
|-------------|------------------------|
| svchp (IN)     | Service context handle.                 |
| errhp (IN)     | An error handle to obtain diagnostic information when an error occurs. |
| flags (IN)     | Flags for global transactions.          |

Some parameters currently support the following values only:

|Parameter |Valid Values |
|--------|------------|
| flags  | TMNOFLAGS  |
