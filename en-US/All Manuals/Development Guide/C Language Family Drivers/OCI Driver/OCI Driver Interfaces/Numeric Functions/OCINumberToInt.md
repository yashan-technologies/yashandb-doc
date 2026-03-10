## Functionality Introduction

The OCINumberToInt function is used to convert an OCI NUMBER to an integer type.

## Function Declaration

```c
sword OCINumberToInt ( OCIError              *err,
                       const OCINumber       *number,
                       uword                 rsl_length,
                       uword                 rsl_flag, 
                       void                  *rsl );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- |---------------------------------------------------------------------------------------------------|
| err (IN/OUT)      | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| number (IN)       | The OCI NUMBER to be converted.                                                                        |
| rsl_length (IN)   | The length of the integer.                                                                              |
| rsl_flag (IN)     | The flag specifying the integer sign; OCI_NUMBER_UNSIGNED represents an unsigned value, OCI_NUMBER_SIGNED represents a signed value.                                     |
| rsl (OUT)         | Pointer to the result.                                                                                  |

Currently, only the following values are supported for some parameters:

|Parameter |Valid Values |
| ---- |---------------------------------------------------------|
| rsl_flag (IN)     | OCI_NUMBER_UNSIGNED represents an unsigned value<br />OCI_NUMBER_SIGNED represents a signed value  |