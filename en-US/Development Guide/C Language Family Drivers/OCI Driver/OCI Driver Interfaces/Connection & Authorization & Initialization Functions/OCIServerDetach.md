## Functionality Introduction

The OCIServerDetach function is used to remove the data source access path.

## Function Declaration

```c
sword OCIServerDetach ( OCIServer   *srvhp,
                        OCIError    *errhp,
                        ub4          mode ); 
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------------ |
| srvhp (IN)      | The handle of the initialized server context.<br />This context is reset to an uninitialized state, and the handle is not deallocated. |
| errhp (IN/OUT)  | An error handle.<br />When an error occurs, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information. |
| mode (IN)       | Specifies the operation mode.                             |

Some parameters currently only support the following values:

|Parameter |Valid Values |
|------|-------------|
| mode | OCI_DEFAULT |
