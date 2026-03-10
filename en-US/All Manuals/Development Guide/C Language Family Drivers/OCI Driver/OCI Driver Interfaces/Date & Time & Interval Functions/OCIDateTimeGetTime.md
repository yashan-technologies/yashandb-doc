## Functionality Overview

The OCIDateTimeGetTime function is used to retrieve the time (hour, minute, second, fractional second).

## Function Declaration

```c
sword OCIDateTimeGetTime ( void          *hndl, 
                           OCIError      *err, 
                           OCIDateTime   *datetime, 
                           ub1           *hour,
                           ub1           *min, 
                           ub1           *sec, 
                           ub4           *fsec );
```

## Parameter Description

|Parameter Name |Description |
| ------------- | ------------------------------------------------------------ |
| hndl (IN)      | User session handle or environment handle.                  |
| err (IN/OUT)   | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| datetime (IN)  | Pointer to the OCIDateTime descriptor from which time information is retrieved. |
| hour (OUT)     | Retrieved hour value.                                       |
| min (OUT)      | Retrieved minute value.                                     |
| sec (OUT)      | Retrieved second value.                                     |
| fsec (OUT)     | Retrieved fractional second value.                           |