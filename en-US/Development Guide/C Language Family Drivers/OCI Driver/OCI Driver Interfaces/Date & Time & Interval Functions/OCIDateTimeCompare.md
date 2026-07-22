## Functionality Overview

The OCIDateTimeCompare function is used to compare two datetime values.

## Function Declaration

```c
sword OCIDateTimeCompare( void              *hndl, 
                          OCIError          *err, 
                          const OCIDateTime *date1,
                          const OCIDateTime *date2,  
                          sword             *result );
```

## Parameter Description

|Parameter Name |Description |
|---------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)        | User session handle or environment handle.                                                          |
| err (IN/OUT)     | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| date1 (IN)       | A pointer to the OCIDateTime descriptor from which to retrieve date information.                   |
| date2 (IN)       | A pointer to the OCIDateTime descriptor from which to retrieve date information.                   |
| result (OUT)     | Comparison result.<br />Returns 1 if date1 > date2; returns 0 if date1 = date2; otherwise returns -1.                     |