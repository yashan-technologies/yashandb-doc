## Functionality Introduction

The OCISessionEnd function is used to terminate the user session context.

## Function Declaration

```c
sword OCISessionEnd ( OCISvcCtx       *svchp,
                      OCIError        *errhp,
                      OCISession      *usrhp,
                      ub4              mode );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------------ |
| srvhp (IN/OUT)   | Service context handle.                                       |
| errhp (IN/OUT)   | An error handle.<br />When an error occurs, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information. |
| usrhp (IN)       | Cancels the authentication for this user.                   |
| mode (IN)        | Specifies the operation mode.                                 |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ----- | -------------- |
| mode  | OCI_DEFAULT    |
