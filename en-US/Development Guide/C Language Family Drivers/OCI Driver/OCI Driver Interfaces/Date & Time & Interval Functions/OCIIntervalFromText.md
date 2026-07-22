## Functionality Introduction

The OCIIntervalFromText function is used to parse a time interval string, converting the given interval string into the interval it represents. The return value result is of the type that corresponds to this interval.

## Function Declaration

```c
sword OCIIntervalFromText ( void           *hndl, 
                            OCIError       *err, 
                            const OraText  *inpstring,
                            size_t          str_len,
                            OCIInterval    *result );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------------ |
| hndl (IN)        | User session handle or environment handle.                   |
| err (IN/OUT)     | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be retrieved by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| inpstring (IN)   | Input string.                                                |
| str_len (IN)     | Length of the input string.                                  |
| result (OUT)     | Resulting interval.                                         |