## Functionality Overview

The OCIHandleAlloc function is used to return an allocated and initialized handle.

## Function Declaration

```c
sword OCIHandleAlloc ( const void    *parenth,
                       void         **hndlpp, 
                       ub4            type, 
                       size_t         xtramem_sz,
                       void         **usrmempp );
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------------------------------------------ |
| parenth (IN)     | Environment handle.                                          |
| hndlpp (OUT)     | Returns a handle.                                           |
| type (IN)        | Specifies the type of handle to be allocated.               |
| xtramem_sz (IN)  | Specifies the amount of user memory to be allocated, reserved parameter. |
| usrmempp (OUT)   | Returns a pointer, reserved parameter.<br />Points to the user memory allocated by the caller with a size of xtramem_sz. |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ---- |----------------------------------------------------------------------------------------------------------------------------------------------------------|
| type | OCI_HTYPE_ERROR<br />OCI_HTYPE_DESCRIBE<br />OCI_HTYPE_SERVER<br />OCI_HTYPE_SESSION<br />OCI_HTYPE_SVCCTX<br />OCI_HTYPE_STMT<br />OCI_HTYPE_SPOOL<br/>OCI_HTYPE_TRANS |
