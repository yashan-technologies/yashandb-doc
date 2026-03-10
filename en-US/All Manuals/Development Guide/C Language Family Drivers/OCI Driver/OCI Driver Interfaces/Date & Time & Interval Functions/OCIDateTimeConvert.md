## Functionality Introduction

The OCIDateTimeConvert function is used to convert a date type to another date type.

## Function Declaration

```c
sword OCIDateTimeConvert( void        *hndl, 
                          OCIError    *err, 
                          OCIDateTime *indate,
                          OCIDateTime *outdate);
```

## Parameter Description

|Parameter Name |Description |
|---------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)         | User session handle or environment handle.                                                              |
| err (IN/OUT)      | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Get diagnostic information by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| indate (IN)       | Pointer to the OCIDateTime descriptor from which date information is retrieved.                          |
| outdate (OUT)     | Pointer to the OCIDateTime descriptor that holds the converted date information.                        |