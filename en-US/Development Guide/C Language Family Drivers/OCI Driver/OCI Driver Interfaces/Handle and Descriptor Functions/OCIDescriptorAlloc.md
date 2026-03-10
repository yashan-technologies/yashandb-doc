## Overview of Functionality

The OCIDescriptorAlloc function is used to allocate storage space to hold a descriptor or LOB locator.

## Function Declaration

```c
sword OCIDescriptorAlloc ( const void    *parenth,
                           void         **descpp, 
                           ub4            type,
                           size_t         xtramem_sz,
                           void         **usrmempp);
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------------------------------------------ |
| parenth (IN)     | Environment handle.                                         |
| descpp (OUT)     | Returns the descriptor or LOB locator of the specified type. |
| type (IN)        | Specifies the type of descriptor or LOB locator to allocate. |
| xtramem_sz (IN)  | Specifies the amount of user memory to allocate for application use during the descriptor's lifetime, reserved parameter. |
| usrmempp (OUT)   | Returns a pointer, reserved parameter.<br />Points to user memory allocated by the caller for size xtramem_sz during the descriptor's lifetime. |

Currently, some parameters only support the following values:

|Parameter |Valid Values |
| ---- |------------------------------------------------------------------------------------|
| type | OCI_DTYPE_DATE<br/>OCI_DTYPE_TIMESTAMP<br/>OCI_DTYPE_INTERVAL_DS<br/>OCI_DTYPE_LOB |
