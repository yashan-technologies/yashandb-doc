## Functionality Introduction

The OCITransRollback function is used to roll back the current transaction.

## Function Declaration

```c
sword OCITransRollback ( void         *svchp, 
                         OCIError     *errhp,
                         ub4           flags );
```

## Parameter Description

|Parameter Name |Description |
| ---------- | ------------------------------------------ |
| svchp (IN)     | Service context handle.                           |
| errhp (IN)     | An error handle that can be used to obtain diagnostic information in case of an error. |
| flags (IN)     | Flags for one-phase commit optimization in global transactions; the default parameter value is OCI_DEFAULT. |

Some parameters currently support only the following values:

|Parameter |Valid Values |
| ----- | ----------- |
| flags | OCI_DEFAULT |
