## Functionality Introduction

The OCIDateTimeAssign function is used to assign a timestamp.

## Function Declaration

```c
sword OCIDateTimeAssign(void              *hndl, 
                        OCIError          *err, 
                        const OCIDateTime *from, 
                        OCIDateTime       *to);
```

## Parameter Description

|Parameter Name |Description |
|--------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)      | User session handle or environment handle.                                                    |
| err (IN/OUT)   | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Retrieve diagnostic information by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| from (IN)      | A pointer to an OCIDateTime descriptor from which the time information is retrieved.           |
| to (OUT)       | The assigned timestamp.                                                                         |