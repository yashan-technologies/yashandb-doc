## Functionality Introduction

The OCINumberFromInt function is used to convert a real (floating-point) number to OCI NUMBER type.

## Function Declaration

```c
sword OCINumberFromReal ( OCIError           *err,
                          const void         *rnum,
                          uword              rnum_length,
                          OCINumber          *number );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- |---------------------------------------------------------------------------------------------------|
| err (IN/OUT)      | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />You can obtain diagnostic information by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| rnum (IN)         | Pointer to the floating-point number to be converted.                                                          |
| rnum_length (IN)  | Length of the floating-point number.                                                                           |
| number (OUT)      | Converts the given floating-point number to OCI NUMBER type.                                                  |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ---- |----|
| rnum_length | sizeof({float / double}) |
