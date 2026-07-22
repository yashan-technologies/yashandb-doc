## Functionality Overview

The OCIDateTimeSysTimeStamp function is used to obtain the current client SCN.

## Function Declaration

```c
sword OCIDateTimeSysTimeStamp( void        *hndl, 
                               OCIError    *err,
                               OCIDateTime *sys_date );
```

## Parameter Description

|Parameter Name |Description |
|----------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)         | User session handle or environment handle.                                                             |
| err (IN/OUT)      | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Retrieve diagnostic information by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| datetime (OUT)    | Pointer to the OCIDateTime descriptor, which contains the retrieved date information.                                                                   |