## Functionality Overview

The OCIDescriptorFree function is used to free a previously allocated descriptor.

Before calling the OCIDescriptorFree function, the [OCILobFreeTemporary](../LOB Functions/OCILobFreeTemporary) function must first be called.

## Function Declaration

```c
sword OCIDescriptorFree ( void     *descp,
                          ub4       type );
```

## Parameter Description

|Parameter Name |Description |
| ---------- | -------------------- |
| descp (IN)     | The allocated descriptor.     |
| type (IN)      | Specifies the type of storage to be freed. |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ---- | ------------------------------------------------------------ |
| type | OCI_DTYPE_TIMESTAMP<br/>OCI_DTYPE_INTERVAL_DS<br/>OCI_DTYPE_LOB<br/>OCI_DTYPE_PARAM |
