## Functionality Introduction

The OCINumberFromInt function is used to convert an integer to OCI NUMBER type.

## Function Declaration

```c
sword OCINumberFromInt ( OCIError            *err,
                         const void          *inum, 
                         uword                inum_length,
                         uword                inum_s_flag,
                         OCINumber           *number );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- |-----------------------------------------------------------------------------|
| err (IN/OUT)      | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| inum (IN)         | Pointer to the integer to be converted.                                       |
| inum_length (IN)  | Length of the integer.                                                         |
| inum_s_flag (IN)  | Flag indicating the sign of the integer; OCI_NUMBER_UNSIGNED represents unsigned values, OCI_NUMBER_SIGNED represents signed values. |
| number (OUT)      | The given integer converted to OCI NUMBER type.                               |

Currently, some parameters only support the following values:

|Parameter |Valid Values |
| ---- |---------------------------------------------------------|
| inum_s_flag    | OCI_NUMBER_UNSIGNED represents unsigned values<br />OCI_NUMBER_SIGNED represents signed values |