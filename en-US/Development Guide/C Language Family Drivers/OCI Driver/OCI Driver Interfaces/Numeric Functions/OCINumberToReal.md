## Functionality Introduction

The OCINumberToReal function is used to convert OCI NUMBER to a real (floating-point) type.

## Function Declaration

```c
sword OCINumberToReal ( OCIError            *err,
                        const OCINumber     *number,
                        uword               rsl_length,
                        void                *rsl );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- |-----------------------------------------------------------------------------|
| err (IN/OUT)     | Error handle.<br />If an error occurs, it will be recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| number (IN)     | The OCI NUMBER to be converted.                                          |
| rsl_length (IN) | The length of the real (floating-point) number.                         |
| rsl (OUT)       | Pointer to the result.                                                  |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ---- |---------------|
| rsl_length | sizeof({float / double})  |
