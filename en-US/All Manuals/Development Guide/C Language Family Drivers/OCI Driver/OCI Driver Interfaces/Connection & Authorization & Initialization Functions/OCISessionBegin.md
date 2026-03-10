## Functionality Introduction

The OCISessionBegin function is used to create and start a user session.

## Function Declaration

```c
sword OCISessionBegin ( OCISvcCtx     *svchp,
                        OCIError      *errhp,
                        OCISession    *usrhp,
                        ub4            credt,
                        ub4            mode );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------------ |
| svchp (IN)       | Handle for the service context.                         |
| errhp (IN)       | An error handle.<br />When an error occurs, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to retrieve diagnostic information. |
| usrhp (IN/OUT)   | Handle for the user session context, which is initialized by this call. |
| credt (IN)       | Specifies the type of credentials used to establish the user session. |
| mode (IN)        | Specifies the operation mode.                           |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ----- | -------------- |
| credt | OCI_CRED_RDBMS |
| mode  | OCI_DEFAULT    |
