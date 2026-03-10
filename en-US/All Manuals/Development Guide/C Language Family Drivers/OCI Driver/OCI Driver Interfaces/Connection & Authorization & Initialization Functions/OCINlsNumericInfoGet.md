## Functionality Introduction

The OCINlsNumericInfoGet function is used to obtain language information for an OCI environment or session handle.

## Function Declaration

```c
sword OCINlsNumericInfoGet ( void        *hndl, 
                             OCIError    *errhp, 
                             sb4         *val, 
                             ub2          item );
```

## Parameter Description

|Parameter Name |Description |
| ----------- |----------------------------------------------------------------------------------------------------------------------------------------|
| hndl (IN/OUT)  | Handle of the service context.                                                                                                                  |
| errhp (IN/OUT) | An error handle.<br />When an error occurs, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to retrieve diagnostic information.                       |
| val (OUT)      | Pointer to an output numeric variable.                                                                                                         |
| item (IN)      | Specifies the item to obtain from the OCI environment handle, which can be one of the following values:<br />OCI_NLS_CHARSET_MAXBYTESZ: Maximum character byte size of the character set for the OCI environment or session handle.<br />OCI_NLS_CHARSET_FIXEDWIDTH: Byte size of characters for a fixed-width character set.<br />0: Indicates a variable-width character set. |

Some parameters currently support the following values:

|Parameter |Valid Values |
| ----- |-------------|
| hndl      | envhp handle        |