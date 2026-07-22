## Functionality Overview

The OCIDateTimeGetDate function is used to retrieve the date (year, month, day).

## Function Declaration

```c
sword OCIDateTimeGetDate ( void               *hndl,
                           OCIError           *err, 
                           const OCIDateTime  *datetime,
                           sb2                *year, 
                           ub1                *month, 
                           ub1                *day );
```

## Parameter Description

|Parameter Name |Description |
| ------------- | ------------------------------------------------------------ |
| hndl (IN)      | User session handle or environment handle.                  |
| err (IN/OUT)   | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| datetime (IN)  | Pointer to the OCIDateTime descriptor from which date information is retrieved. |
| year (OUT)     | Retrieved year value.                                       |
| month (OUT)    | Retrieved month value.                                      |
| day (OUT)      | Retrieved day value.                                        |