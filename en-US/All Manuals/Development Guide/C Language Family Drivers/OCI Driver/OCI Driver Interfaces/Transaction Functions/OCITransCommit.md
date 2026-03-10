## Functionality Overview

The OCITransCommit function is used to commit a transaction associated with a specified service context.

## Function Declaration

```c
sword OCITransCommit ( OCISvcCtx    *svchp, 
                       OCIError     *errhp,
                       ub4           flags );
```

## Parameter Description

|Parameter Name |Description |
| ---------- | ------------------------------------------ |
| svchp (IN)     | Service context handle.                          |
| errhp (IN)     | An error handle that can be used to retrieve diagnostic information when an error occurs. |
| flags (IN)     | Flags for one-phase commit optimization in global transactions. |

Currently, some parameters only support the following values:

|Parameter |Valid Values |
| ----- | ----------- |
| flags | OCI_DEFAULT |
