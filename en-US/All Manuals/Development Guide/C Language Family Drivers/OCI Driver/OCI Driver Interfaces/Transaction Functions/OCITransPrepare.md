## Functionality Overview

The OCITransPrepare function is used to prepare the global transaction to be committed.

## Function Declaration

```c
sword OCITransPrepare ( OCISvcCtx    *svchp, 
                       OCIError     *errhp,
                       ub4           flags );
```

## Parameter Description

|Parameter Name |Description |
|-------------|------------------------|
| svchp (IN)     | Service context handle.                    |
| errhp (IN)     | An error handle that provides diagnostic information when an error occurs. |
| flags (IN)     | Flags for the global transaction.          |

Currently, some parameters only support the following values:

|Parameter |Valid Values |
|--------|-----------|
| flags  | TMNOFLAGS |
