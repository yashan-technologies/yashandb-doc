## Functionality Overview

The OCIDateTimeGetTimeZoneOffset function is used to get the time zone of the current time type.

## Function Declaration

```c
sword OCIDateTimeGetTimeZoneOffset( void              *hndl,
                                    OCIError          *err,
                                    const OCIDateTime *datetime,
                                    sb1               *hr,
                                    sb1               *mm);
```

## Parameter Description

|Parameter Name |Description |
|---------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)           | User session handle or environment handle.                                                               |
| err (IN/OUT)        | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Use [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information. |
| datetime (IN)       | A pointer to the OCIDateTime descriptor from which to retrieve date information.                          |
| hr (OUT)            | The hour offset of the time zone.                                                                        |
| mm (OUT)            | The minute offset of the time zone.                                                                       |